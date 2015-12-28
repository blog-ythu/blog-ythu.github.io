---
layout: post
comments: true
title: "Using MSXML to read/write XML files"
excerpt: ""
date: 2012-07-11 11:00:00
mathjax: true
---

In [previous post](http://blog-ythu.github.io/2012/06/18/using-Libxml2/), I talked about how to use [Libxml2](http://www.xmlsoft.org/) library to read/write xml files. Actually, there are many [Free C or C++ XML Parser Libraries](http://lars.ruoff.free.fr/xmlcpp/). From now no, I will turn to [MSXML](http://msdn.microsoft.com/en-us/library/ms763742.aspx) instead because of Libxml2's poor support for Windows-x64 systems.

In the followings, I will show how to use MSXML to read/write xml files.

<!-- add TOC here -->
<div id="renderIn"></div>

## Pre-work:
- To install MSXML is quite easy, you just need to install Windows SDK, where it is integrated.
- Add `#import <msxml3.dll>` to the beginning part of your code.
- Use the following xml files as an example:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Appearance_model>
        <name>xxx</name>
        <Feature index="6" weight="0.017783"/>
        <Feature index="10" weight="0.003343"/>
    </Appearance_model>
    ```

## Read from xml files:
For example, we want to load the above xml data to a `vector<pair<int, float>>` (`name` to a string), do like the following code, where input parameter gives the path of above xml file.

```cpp
vector<pair<int, float>> load_appearance_model_from_xml(string file, string & name)
{
    vector<pair<int, float>> app_model;

    CoInitialize(NULL);

    //read XML
    MSXML2::IXMLDOMDocumentPtr spXMLDoc;
    spXMLDoc.CreateInstance(__uuidof(MSXML2::DOMDocument30));
    spXMLDoc->load(file.c_str());

    MSXML2::IXMLDOMElementPtr spRoot = spXMLDoc->documentElement; //root node
    if (spRoot->nodeName == (_bstr_t)"Appearance_model")
    {
        MSXML2::IXMLDOMNodeListPtr spNodeList = spRoot->childNodes;

        // traverse child's nodes
        for (long i = 0; i != spNodeList->length; ++i)
        {
            MSXML2::IXMLDOMNodePtr spNode = spNodeList->item[i];
            if (spNode->nodeName == (_bstr_t)"name")
            {
                name = (char *) spNode->Gettext();
            }
            else if (spNode->nodeName == (_bstr_t)"Feature")
            {
                int index;
                float weight;

                // traverse node's attributes
                MSXML2::IXMLDOMNamedNodeMapPtr spNameNodeMap = spNode->attributes;
                for (long j = 0; j != spNameNodeMap->length; ++j)
                {
                    MSXML2::IXMLDOMNodePtr spNode2 = spNameNodeMap->item[j];

                    if (spNode2->nodeName == (_bstr_t)"index")
                        index = (int)spNode2->nodeValue;
                    else if (spNode2->nodeName == (_bstr_t)"weight")
                        weight = (float)spNode2->nodeValue;
                }

                app_model.push_back(make_pair(index, weight));
            }
        }
    }

    spRoot.Release();
    spXMLDoc.Release();
    CoUninitialize();    

    return app_model;
}
```

## Write to xml files:
Do like the following code, where input parameter file gives the file name that you want to save.

```c++
void write_appearance_model_to_xml(string file, vector<pair<int, float>> app_model, string name)
{
    CoInitialize(NULL);

    //read XML
    MSXML2::IXMLDOMDocumentPtr spXMLDoc;
    MSXML2::IXMLDOMElementPtr  spRoot;
    HRESULT hr = spXMLDoc.CreateInstance(__uuidof(MSXML2::DOMDocument30));

    if (!SUCCEEDED(hr))
        printf("Unable to create xml file.\n");
    else
    {
        spXMLDoc->raw_createElement((_bstr_t)"Appearance_model", &spRoot);
        spXMLDoc->raw_appendChild(spRoot, NULL);

        MSXML2::IXMLDOMElementPtr childNode2;
        spXMLDoc->raw_createElement((_bstr_t)"name", &childNode2);
        childNode2->put_text((_bstr_t)xxx);
        spRoot->appendChild(childNode2);

        for (int i=0; i<app_model.size(); i++)
        {
            MSXML2::IXMLDOMElementPtr childNode;
            spXMLDoc->raw_createElement((_bstr_t)"Feature", &childNode);

            childNode->setAttribute("index", app_model[i].first);
            childNode->setAttribute("weight", app_model[i].second);

            spRoot->appendChild(childNode);
        }

        spXMLDoc->save(file.c_str());
    }

    spRoot.Release();
    spXMLDoc.Release();
    CoUninitialize();
}
```

**Note:** Above writing xml with `<name>xxx</name> style is not fully tested.
