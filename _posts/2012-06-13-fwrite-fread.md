---
layout: post
comments: true
title: "fwrite/fread for different data types"
excerpt: "fwrite/fread for complex types, e.g. cv::Mat, struct etc."
date: 2012-06-13 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="renderIn"></div>

We use `fwrite` to write formated data to disk and use `fread` to later read them back from disk. Different data types are very different in using `fwrite/fread`. The following shows how to do for `vector` (simple), `Mat`, `vector<Mat>`, `struct` (simple), `struct` (complex).

## for `vector` (simple):
1. `fwrite`:

    ```cpp
    void write_vector(string file, vector<int> a)
    {
        // write data to file
        FILE *fo;
        fo = fopen(file.c_str(), "wb");
        //size_element = result[0].elemSize()*result[0].total();
        fwrite(&a[0], a.size(), sizeof(int), fo);
        fclose(fo);
    }
    ```
2. `fread`:

    ```cpp
    vector<int> load_vector(string file, int size)
    {
        vector<int> result;
        result.resize(size);

        // read data from file
        FILE *fi;
        fi = fopen(file.c_str(), "rb");

        fread( &result[0], sizeof(int), result.size(), fi );
        fclose(fi);

        return result;
    }
    ```
3. Usage:

    ```cpp
    vector<int> a;
    a.push_back(0);
    a.push_back(1);
    a.push_back(2);
    write_vector("vector.dat", a);
    vector<int> b = load_vector("vector.dat", 3);
    ```

## for `Mat`:
1. `fwrite`:

    ```cpp
    void write_mat(string file, Mat a)
    {
        // write data to file
        FILE *fo;
        fo = fopen(file.c_str(), "wb");
        //size_element = result[0].elemSize()*result[0].total();
        fwrite(a.ptr(), 1, (size_t)(a.elemSize()*a.total()), fo);
        fclose(fo);
    }
    ```
2. `fread`:

    ```cpp
    Mat load_mat(string file, int rows, int cols, int type)
    {
        Mat result(rows, cols, type);

        // read data from file
        FILE *fi;
        fi = fopen(file.c_str(), "rb");
        fread(result.ptr(), (size_t)(result.elemSize()*result.total()), 1, fi);
        fclose(fi);

        return result;
    }
    ```
2. Usage:

    ```cpp
    Mat a = Mat::eye(3, 3, CV_32FC1);
    write_mat("mat.dat", a);
    Mat b = load_mat("mat.dat", 3, 3, CV_32FC1);
    ```

## for `vector<Mat>` (every `Mat` have similar data):
3. `fwrite`:

    ```cpp
    void write_all_images_content_to_disk(vector<Mat> result, string file, size_t & size_mat)
    {
        // write data to file
        FILE *fo;
        fo = fopen(file.c_str(), "wb");
        for (int i=0; i<result.size(); i++)
        {
            Mat stub = result[i];

            size_mat = (size_t)(stub.elemSize()*stub.total());
            fwrite(stub.ptr(), size_mat, 1, fo);
        }
        fclose(fo);
    }
    ```
3. `fread`:

    ```cpp
    vector<Mat> load_images_content_from_disk(string file, size_t size_mat, int size)
    {
        vector<Mat> result;
        result.resize(size);

        // read data from file
        FILE *fi;
        fi = fopen(file.c_str(), "rb");
        for (int i=0; i<size; i++)
        {
            Mat stub(WIDTH_IMAGE, HEIGHT_IMAGE, CV_8UC3);

            fread(stub.ptr(), size_mat, 1, fi);

            result.push_back(stub);
        }
        fclose(fi);

        return result;
    }
    ```
3. Usage:

    ```cpp
    // for vector<Mat> IMAGES_CONTENT;
    size_t size_mat;
    write_all_images_content_to_disk(IMAGES_CONTENT, "images.dat", size_mat);
    vector<Mat> stub = load_images_content_from_disk("images.dat", size_mat, IMAGES_CONTENT.size());
    ```

## for `struct` (simple):
Take the following struct as an example:

```cpp
struct PERSON
{
    char name[30];
    int age;
};
```

4. `fwrite`:

    ```cpp
    void write_struct(string file, PERSON a)
    {
        // write data to file
        FILE *fo;
        fo = fopen(file.c_str(), "wb");
        fwrite(&a, sizeof(struct PERSON), 1, fo);
        fclose(fo);
    }
    ```
4. `fread`:

    ```cpp
    void load_struct(string file, PERSON & result)
    {
        // read data from file
        FILE *fi;
        fi = fopen(file.c_str(), "rb");
        fread(&result, sizeof(struct PERSON), 1, fi);
        fclose(fi);
    }
    ```
4. Usage:

    ```cpp
    PERSON a;
    strcpy(a.name, "Tommy");
    a.age = 25;

    write_struct("person.dat", a);
    PERSON b;
    load_struct("person.dat", b);
    ```

## for `struct` (contains `vector` and `Mat`):
Take the following struct as an example:

```cpp
struct FEATURE_RECT
{
    Rect rect;

    vector<float> color_hist;
    vector<float> hog;
    Mat region_mat;
};
```
5. `fwrite`:

    ```cpp
    void write_struct(string file, FEATURE_RECT a)
    {
        // write data to file
        FILE *fo;
        fo = fopen(file.c_str(), "wb");
        fwrite(&a, sizeof(struct FEATURE_RECT), 1, fo);
        fwrite(&a.color_hist[0], sizeof(float), a.color_hist.size(), fo);
        fwrite(&a.hog[0], sizeof(float), a.hog.size(), fo);
        fclose(fo);
    }
    ```
5. `fread`:

    ```cpp
    void load_struct(string file, FEATURE_RECT & result)
    {

        // read data from file
        FILE *fi;
        fi = fopen(file.c_str(), "rb");
        fread(&result, sizeof(struct FEATURE_RECT ), 1, fi);
        fread(&result.color_hist[0], sizeof(float), result.color_hist.size(), fi);
        fread(&result.hog[0], sizeof(float), result.hog.size(), fi);
        fclose(fi);
    }
    ```
5. Usage:

    ```cpp
    FEATURE_RECT test;
    test.rect = Rect(1,2,100,200);
    test.color_hist.push_back(0);
    test.hog.push_back(1);
    test.region_mat = Mat::eye(3,3,CV_32FC1);

    write_struct("struct.dat", test);
    FEATURE_RECT test2;
    load_struct("struct.dat", test2);
    ```
