---
title: "【Python】Excelの表をはてな記法に変換するPythonスクリプト"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

タイトルの通り、Excelの表（複数の表もOK）をはてな記法の表（ | と |\* を使ったやつ）に変換するPythonプログラムを書いてみました。  
二重for文をぶん回しているところもあって拙いコードですが......m(\_\_)m

* [プログラム](#プログラム)
* [実行](#実行)

### プログラム

import openpyxl as px
import numpy as np

class Excel_to_HATENA:
    SpaceText = "  "  # 表の中に何もテキストがないセルがあった場合、代わりに挿入される文字列
    isMultiTable = True   # True:  Excelファイルの中の「#番号」の部分で区切ってそれぞれを一つの表として処理する。
                          # False：Excelファイル全体を一つの表と考えて処理する
    def __init__(self, filename="table1.xlsx"):
        self.filename = filename
        self.sheet_all = None
        self.HATENA_txt_list = []
        self.coloed_num = 0
        self.colored_axis = 0

    def LoadTables(self):
        """ Excelファイルを読み込み、二次元配列に変換し、sheet_allに値を代入 """
        book = px.load_workbook(self.filename)
        active_sheet = book.active
        myTable = np.array([])
        x_size = 0
        y_size = 0

        for column in active_sheet.columns:
            oneData_array = np.array([])
            row_num = 0
            for cell in column:
                oneData_array = np.append(oneData_array, cell.value)
                row_num += 1
            x_size = row_num
            myTable = np.append(myTable, oneData_array)

        y_size = int(len(myTable) / x_size)
        self.sheet_all = myTable.reshape((y_size, x_size))


    def _makeTables(self):
        """ LoadTables関数で作成した配列をもとに、表を分割する。 """
        split_row_nums = []
        x_size = np.shape(self.sheet_all)[0]
        y_size = np.shape(self.sheet_all)[1]

        myTable3 = self.sheet_all.T
        for i in range(x_size):
            for j in range(y_size):
                if "#" in str(myTable3[j][i]) and self.isMultiTable:
                    print("表を分割します -> " + str(j))
                    split_row_nums.append(j)

        Splited = np.split(myTable3, split_row_nums)

        Table_list = []
        for OneTable in Splited:
            Table_list.append(self._deleteExtraRegion(OneTable))
        return Table_list


    def _deleteExtraRegion(self, in_array):
        """
        input -> in_array: 表の一つの二次元配列（欲しいデータの周りのNone部分も含む）
        output -> in_array: 入力のin_arrayの不要部分（Noneの行/列）を削除したもの
        """
        # 要素数が足りない列がないかをチェック(縦)
        x_size = np.shape(in_array)[1]
        y_size = np.shape(in_array)[0]

        for i in range(x_size):
            for j in range(y_size):
                if "#" in str(in_array[j][i]) and self.isMultiTable:
                      in_array[j][i] = None

        delete_num = 0
        error_num = np.count_nonzero(in_array == None, axis=0)  #TODO: in_array is None と書くと思った通りの動作ができない
        for (i, error_num2) in enumerate(error_num):
            if error_num2 == y_size:
                in_array = np.delete(in_array, i - delete_num, axis=1)
                x_size -= 1
                delete_num += 1

        #要素数が足りない列がないかをチェック(横)
        delete_num = 0
        error_num = np.count_nonzero(in_array == None, axis=1)
        for (i, error_num2) in enumerate(error_num):
            if error_num2 == x_size:
                in_array = np.delete(in_array, i - delete_num, axis=0)
                y_size -= 1
                delete_num += 1
        return in_array


    def _makeHATENA_table(self, data_array):
        """一つの二次元配列をはてな記法に変換する"""
        if len(data_array) == 0:
            print("no data!!")
            return ""
        OutputText = ""
        for (i, one_data) in enumerate(data_array):
            OutputText += "|"
            for (j, value) in enumerate(one_data):
                if (j == self.coloed_num and self.colored_axis == 0) or (i == self.coloed_num and self.colored_axis == 1):
                    OutputText += "*"
                if value is None:
                    OutputText += self.SpaceText + "|"
                else:
                    OutputText += str(value) + "|"
            OutputText += "\n"
        return OutputText


    def converter(self):
        self.LoadTables()
        table_list = self._makeTables()
        self.HATENA_txt_list = [self._makeHATENA_table(text) for text in table_list]
        return self.HATENA_txt_list


A = Excel_to_HATENA()
text_list = A.converter()

for text in text_list:
    print("\n\n########################################")
    print(text)

### 実行

試しに、下の画像のようなExcelファイルを作って、「table.xlsx」で保存します。  
![f:id:pythonjacascript:20200321203129j:plain](/images/ppythonjacascript2020032120200321203129.jpg "f:id:pythonjacascript:20200321203129j:plain")

そして、上のプログラムを実行すると、下のように表示されるはずです。

(出力)
表を分割します -> 0
表を分割します -> 8
表を分割します -> 15

########################################
|*項目A|項目B|項目C|項目D|
|*a|0|A|あ|
|*b|1|B|い|
|*c|2|C|う|
|*d|3|D|え|
|*e|4|E|お|
|*  |  |  |か|

########################################
|*X|Y|Z|項目D|
|*aa|101|AA|ああ|
|*bb|102|BB|いい|
|*cc|103|CC|うう|
|*dd|104|DD|ええ|

########################################
|*Data1|Data2|
|*aaa|I|
|*bbb|II|
|*ccc|III|
|*ddd|X|

  
これを実際にブログの編集画面にコピー＆ペーストすると、以下のように表示されます。

########################################

| 項目A | 項目B | 項目C | 項目D |
| --- | --- | --- | --- |
| a   | 0   | A   | あ   |
| b   | 1   | B   | い   |
| c   | 2   | C   | う   |
| d   | 3   | D   | え   |
| e   | 4   | E   | お   |
| |   |     | か   |     |

########################################

| X  | Y   | Z  | 項目D |
| -- | --- | -- | --- |
| aa | 101 | AA | ああ  |
| bb | 102 | BB | いい  |
| cc | 103 | CC | うう  |
| dd | 104 | DD | ええ  |

########################################

| Data1 | Data2 |
| ----- | ----- |
| aaa   | I     |
| bbb   | II    |
| ccc   | III   |
| ddd   | X     |