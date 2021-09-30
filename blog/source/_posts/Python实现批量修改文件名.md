---
title: Python实现批量修改文件名
date: 2020-05-02 04:37:22
tags: [Python]
---

> 今天的小题大做

{% asset_img 1.png %}

根据要求我需要将很多人的图片名称由原先的

 `身份证号_姓名_班级_学号_性别`格式

 改为`编号_姓名`的格式

并给出了新的编号表（我另存成了csv格式的文件）

本来手动改也是几分钟改完的任务量，但是一个个的点来点去的那种操作怎么能从我的手里出现呢？

上代码：
``` Python
import os
import csv

path_name = '/Users/wangshubo/Desktop/test1'
# path_name :表示你需要批量改的文件夹

list = []
index = 0


def readCSV2List(filePath):
    try:
        file = open(filePath, 'r', encoding='utf-8')  # 读取以utf-8
        context = file.read()  # 读取成str
        list_result = context.split("\n")  # 以回车符\n分割成单独的行
        # 每一行的各个元素是以【,】分割的，因此可以
        length = len(list_result)
        for i in range(length):
            list_result[i] = list_result[i].split(",")
        return list_result
    except Exception:
        print("文件读取转换失败，请检查文件路径及文件编码是否正确")
    finally:
        file.close();  # 操作完成一定要关闭


print(readCSV2List('test1.csv'))
csvlist = readCSV2List('test1.csv')#将csv文件保存到数组
for i in csvlist:
    if len(i) < 6:
        csvlist.remove(i)
    print(len(i))
print(len(csvlist))

for item1 in os.listdir(path_name):  # 进入到文件夹内，对每个文件进行循环遍历

    # 照片命名要求：编号_姓名.jpg。（切记中间不要有空格且必须用短下划线隔开）
    file_name = item1
    print(file_name)
    print(len(file_name.split("_")))
    for index1 in csvlist: #此处使用了第二层循环，执行效率非常的低，临时抱佛脚只能先这样了
        print(index1[2])
        print(file_name.split("_")[1].strip())
        print(index1[2].strip() == file_name.split("_")[1].strip())
        if index1[2].strip() == file_name.split("_")[1].strip(): #通过比对姓名是否一致来重命名图片
            os.rename(os.path.join(path_name, file_name), os.path.join(path_name, index1[0] + '_' + index1[2] + '.jpg'))

```
 不多说了，备考专升本已经十个多月了，突然写代码来真的是相当生疏，网上搜来搜去七拼八凑，用了整整三个小时。。。



 以此记录今日代码。