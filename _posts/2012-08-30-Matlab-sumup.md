---
layout: post
comments: true
title: "MATLAB sumup"
excerpt: ""
date: 2012-08-30 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="renderIn"></div>

## Get rect from mouse
```matlab
imshow('test.jpg');
rect = getrect;
```

##对话框 dialog box
cc: [matlab GUI之对话框](http://hi.baidu.com/7777777_shu/item/ccb713f20ce99e18d7ff8cda)

##多个string连接
1. `sprintf`

    ```matlab
    file_temp = sprintf('%s\\*.%s', data_pwd, input_type);
    ```
1. strcat

	```matlab
    imgname=strcat(data_pwd,'\\',filearray(i).name);
    ```

##获取文件绝对路径的各个部分 (e.g. 目录path, 文件名, 扩展名)
```matlab
[pathstr,realname,ext] = fileparts('C:\\images\\test.bmp');
```

=>

```matlab
pathstr = 'C:\images\'
realname = 'test'
ext = '.bmp'
```

##UI获取文件名和文件夹

- 文件

    ```matlab
    [filename, pathname] = uigetfile({'*.*';'*.avi';'*.mpg';'*.wmv';'*.asf';'*.asx';'*.mj2'}, 'Select a VIDEO file...');
    video_file_name = fullfile(pathname, filename); % use fullfile for OS independent
    ```
- 文件夹

    ```matlab
    output_images_folder = uigetdir(pwd, 'Select the FOLDER where to save images...')
    ```

##遍历文件夹内所有(指定类型)文件
```matlab
data_pwd = uigetdir;
file_temp = sprintf('%s\\*.%s', data_pwd, input_type);
filearray = dir(file_temp);
s=max(size(filearray));
for i=1:1:s
    imgname=strcat(data_pwd,'\\',filearray(i).name);
    ... ...
end
```

##获取image的长, 宽和channel数
```matlab
img = imread('C:\Users\tommyhu\Desktop\test\frame_0000.bmp');
[h, w, c] = size(img);
```

##遍历video的每一帧
```matlab
mov = VideoReader(video_file_name);
for i=1:1:mov.numberofframes
    b=read(mov,i);
	... ...
end
```

##获取用户输入
- 命令行方式

    ```matlab
    strResponse = input('Do you want more? Y/N [Y]: ', 's');
    ```
- UI方式

    ```matlab
        prompt = { 'from:','to:','step'};
        dlg_title = 'choose images';
        num_lines = [4 1 3]; % can be a scaler number that applies to all
        defAns = {'0', '', '1'};
        options = 'on'; % enable to resize window
        answer = inputdlg(prompt,dlg_title,num_lines,defAns);

        from=str2num(cell2mat(answer(1)));
        to=str2num(cell2mat(answer(2)));
        stepsize=str2num(cell2mat(answer(3)));
    ```

##输出到txt
```matlab
fileID = fopen('faces.txt', 'w');
fprintf(fileID,'[%4.2f,%d,%d,%d]\n', x, y, z, t);
fclose(fileID);
```
