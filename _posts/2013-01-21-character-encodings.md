---
layout: post
comments: true
title: "Character encodings of text files"
excerpt: ""
date: 2013-01-21 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="renderIn"></div>

A text file can be encoded in many different [character encodings](http://en.wikipedia.org/wiki/Character_encoding). There are many [encoding variations](http://en.wikipedia.org/wiki/Windows_code_page) even just for Windows system. Special attention has to be given when handling text files with different character encodings, e.g. if we use `fstream`'s `getline()` to read from a text file (contains Chinese) in `UTF-8`, we will get gibberish characters, while will be correct if the text file is in `ANSI`.

In this post, given a text file, I will show how to get its character encoding and how to convert it from one character encoding to another (has been integrated in [useful functions](https://bitbucket.org/herohuyongtao/useful-functions)).

## Get the character encoding of a text file

Actually, [in many cases](http://stackoverflow.com/questions/9103294/c-how-to-inspect-file-byte-order-mark-in-order-to-get-if-it-is-utf-8), we cannot be sure about which character encoding a file is encoded. In the following, I will only give the method to get a text file's character encoding if its character encoding can only be 4 basic ones, namely `ANSI`, `Unicode`, `Unicode big endian` and `UTF-8 (with BOM)`. These 4 encodings are all Notepad supports.  It cannot guarantee to give the correct answer if not satisfy this, e.g. it will be considered as a ANSI file if it's UTF-8 (without BOM). See the following code (based on [[1]](http://hi.baidu.com/whmtorrent/item/932d270a1dc4787abee97e4f) [[2]](http://stackoverflow.com/questions/9103294/c-how-to-inspect-file-byte-order-mark-in-order-to-get-if-it-is-utf-8)).

```cpp
// 0 - ANSI
// 1 - Unicode
// 2 - Unicode big endian
// 3 - UTF-8
// NOTE: only correct for handling normal encodings (i.e. see NOTEPAD's 4 types)
//        , eg. UTF-8 with BOM, if UTF-8 without BOM, will considered as ANSI
int get_text_file_encoding(const char *filename)
{
    int nReturn = -1;

    unsigned char uniTxt[] = {0xFF, 0xFE};// Unicode file header
    unsigned char endianTxt[] = {0xFE, 0xFF};// Unicode big endian file header
    unsigned char utf8Txt[] = {0xEF, 0xBB};// UTF_8 file header

    DWORD dwBytesRead = 0;
    HANDLE hFile = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile == INVALID_HANDLE_VALUE)
    {
        hFile = NULL;
        CloseHandle(hFile);
        return -1;
    }
    BYTE *lpHeader = new BYTE[2];
    ReadFile(hFile, lpHeader, 2, &dwBytesRead, NULL);
    CloseHandle(hFile);

    if (lpHeader[0] == uniTxt[0] && lpHeader[1] == uniTxt[1])// Unicode file
        nReturn = 1;
    else if (lpHeader[0] == endianTxt[0] && lpHeader[1] == endianTxt[1])//  Unicode big endian file
        nReturn = 2;
    else if (lpHeader[0] == utf8Txt[0] && lpHeader[1] == utf8Txt[1])// UTF-8 file
        nReturn = 3;
    else
        nReturn = 0;   //Ascii

    delete []lpHeader;
    return nReturn;
}
```

## Convert from one character encoding to another

As the example goes in the beginning that if we use `fstream`'s `getline()` to read from a text file (contains Chinese) in `UTF8`, we will get gibberish characters, while will be correct if the text file is in `ANSI`, in the following, only 2 converting methods will be given, namely `UTF-8 (with BOM)` to `ANSI` and `UTF-8 (without BOM)` to `ANSI`. See the following code (based on [[3]](http://m.oschina.net/blog/17457) [[4]](http://blog.csdn.net/afjafjafj2008/article/details/6620617)). Conversions between `UTF-8`, `UTF-16` and `UTF-32` can be seen from [[5]](http://my.oschina.net/zhangzhihao/blog/70462).

```cpp
// change a char's encoding from UTF8 to ANSI
char* change_encoding_from_UTF8_to_ANSI(char* szU8)
{
    int wcsLen = ::MultiByteToWideChar(CP_UTF8, NULL, szU8, strlen(szU8), NULL, 0);
    wchar_t* wszString = new wchar_t[wcsLen + 1];
    ::MultiByteToWideChar(CP_UTF8, NULL, szU8, strlen(szU8), wszString, wcsLen);
    wszString[wcsLen] = '\0';

    int ansiLen = ::WideCharToMultiByte(CP_ACP, NULL, wszString, wcslen(wszString), NULL, 0, NULL, NULL);
    char* szAnsi = new char[ansiLen + 1];
    ::WideCharToMultiByte(CP_ACP, NULL, wszString, wcslen(wszString), szAnsi, ansiLen, NULL, NULL);
    szAnsi[ansiLen] = '\0';

    return szAnsi;
}

// read UTF-8 (with BOM) file and convert it to be in ANSI
void change_text_file_encoding_from_UTF8_with_BOM_to_ANSI(const char* filename)
{
    ifstream infile;
    string strLine="";
    string strResult="";
    infile.open(filename);
    if (infile)
    {
        // the first 3 bytes (ef bb bf) is UTF-8 header flags
        // all the others are single byte ASCII code.
        // should delete these 3 when output
        getline(infile, strLine);
        strResult += strLine.substr(3)+"\n";

        while(!infile.eof())
        {
            getline(infile, strLine);
            strResult += strLine+"\n";
        }
    }
    infile.close();

    char* changeTemp=new char[strResult.length()];
    strcpy(changeTemp, strResult.c_str());
    char* changeResult = change_encoding_from_UTF8_to_ANSI(changeTemp);
    strResult=changeResult;

    ofstream outfile;
    outfile.open(filename);
    outfile.write(strResult.c_str(),strResult.length());
    outfile.flush();
    outfile.close();
}

// read UTF-8 (without BOM) file and convert it to be in ANSI
void change_text_file_encoding_from_UTF8_without_BOM_to_ANSI(const char* filename)
{
    ifstream infile;
    string strLine="";
    string strResult="";
    infile.open(filename);
    if (infile)
    {
        while(!infile.eof())
        {
            getline(infile, strLine);
            strResult += strLine+"\n";
        }
    }
    infile.close();

    char* changeTemp=new char[strResult.length()];
    strcpy(changeTemp, strResult.c_str());
    char* changeResult = change_encoding_from_UTF8_to_ANSI(changeTemp);
    strResult=changeResult;

    ofstream outfile;
    outfile.open(filename);
    outfile.write(strResult.c_str(),strResult.length());
    outfile.flush();
    outfile.close();
}
```
