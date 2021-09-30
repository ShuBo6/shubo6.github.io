---
title: Python实现Excel工作表的分割
date: 2020-05-08 11:21:13
tags: [Python]
---
``` Python
import openpyxl

workbook = openpyxl.load_workbook("test.xlsx")

sheetnames = workbook.sheetnames

# print(sheetnames)

worksheet = workbook["总表"]

# 获取该表相应的行数和列数
CountRows = worksheet.max_row
CountColumns = worksheet.max_column

# print(columns)

worksheetnamelist = []

# 保存大表 数据清单标题
AllTitleList = []
for cell in worksheet.columns:
    # print(cell[0].value)
    AllTitleList.append(cell[0].value)

# 保存大表 第一列（学号）、第二列（姓名）数据
NumList = []
NameList = []
for i in range(2, 31):
    NumList.append(worksheet.cell(row=i, column=1).value)
    NameList.append(worksheet.cell(row=i, column=2).value)

# print(NameList[28])
# 生成工作表计数
count = 0
for start_column in range(3, CountColumns + 1, 14):
    count += 1
    # print(worksheet.cell(row=1, column=start_column).value)
    current_worksheet = workbook.create_sheet()
    current_worksheet.title = worksheet.cell(row=1, column=start_column).value 
    + "-" + worksheet.cell(row=1,column=start_column + 13).value
    current_worksheet.cell(1, 1, "学号")
    current_worksheet.cell(1, 2, "姓名")
    # 填充前两列学号姓名
    for i in range(2, 31):
        current_worksheet.cell(i, 1, NumList[i - 2])
        current_worksheet.cell(i, 2, NameList[i - 2])
    # 遍历日期列名
    for i in range(start_column, start_column + 14):
        print(AllTitleList[i - 1])
        # print(count)
        current_worksheet.cell(1, i - (count - 1) * 14, AllTitleList[i - 1])
        # 填充每日体温数据区域
        for j in range(2, 31):
            current_worksheet.cell(j, i - (count - 1) * 14, worksheet.cell(row=j, column=i).value)

    print("----")
workbook.save(filename="test.xlsx")

```