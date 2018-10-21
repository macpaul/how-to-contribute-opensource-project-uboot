# 程式碼排版風格

不管是什麼大小專案，只要是多人開發，就必須約定一套程式碼排版風格 **\(coding style\)**，減少除錯、閱讀、溝通等等開發成本。程式碼排版風格不只影響工程師打開編輯器閱讀和修改程式的時間。**由於開放原始碼專案，維護者大多數的時間，是花在郵件論壇上直接讀patch檔，來檢查patch是否有錯誤。**因此統一程式碼的排版風格，非常重要。甚至也可以減少未來打patch的除錯時間，會節省很多維護程式品質的時間成本。

u-boot的程式碼排版風格基本上遵循Linux，但是兩者還是會有些稍微不一樣。而且程式碼是根據各專案維護者達成的共識，會隨著開發工具演進而改變或者強化。雖然大家都說寫程式就像是藝術創作，但是程式的本質是科學與工程！就算你發明了再厲害的演算法或者再厲害的功能，沒有遵循大家共同認定的排版格式，開放原始碼社群是不會有人理你做了什麼貢獻的。有的維護人員很忙，也沒空幫你檢查排版是否正確，所以他們甚至第一關連信都不會打開，直接用機器人執行檢查格式的工具來檢查你的patch；機器人會自動回覆你排版不對，請你送新的patch。

不過還好，開放原始碼社群通常都不是存心要找碴的。維護人員用的檢查工具，你也可以用，所以在提交patch之前，用工具先跑過一次檢查，確認你的patch完全遵守排版規則，再送出patch。

以下會針對程式碼排版風格做介紹，但是最新的資訊還是得參照線上文件。當然我們都知道，寫程式永遠比寫文件還要快。也可能新的排版規則已經加入檢查工具，但是線上文件沒有更新。這是很正常的。

## u-boot coding style

**u-boot coding style**: [https://www.denx.de/wiki/U-Boot/CodingStyle](https://www.denx.de/wiki/U-Boot/CodingStyle) \(**必讀**\)

**Linux coding style**: [https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/process/coding-style.rst](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/process/coding-style.rst) \(**必讀**\)

以下列出官網上提及除了**必讀文件**以外，該注意的重點。

* 從其他不同的專案copy過來的原始碼（例如MTD子系統，或者BusyBox來的hush shell）在仔細考量下，或許可以排除在這些規則之外。對這些檔案，考慮到未來升級到新版的可能性，可以允許他們使用原本的coding style。

* 請注意U-boot以C語言實作（一小部分是組合語言）；完全沒有使用C++，請不要在程式碼內用C++風格註解。

* 唯一例外的是某些檔案裡面的 SPDX 標籤（checkpatch.pl會警告你）

也請嚴格遵循下面的排版規則：

* 移除任何行尾空白。
* 垂直對齊請使用TAB字元，不要使用空白。（註：有的編輯器會自動轉換TAB為四個空白或是八個空白，請關閉這個功能）
* 禁止使用DOS的'\r\n' 換行字元。
* 原始檔中禁止使用連續的兩個空白行。
* 原始檔尾禁止檔尾空白行。
* 使用git選項設定`git config --global color.diff auto`協助在git的diff輸出中直接看到空白字元問題。
* 用Emacs的人可以用`M-x whitespace-global-mode`得到細節上可見的回饋。`M-x whitespace-cleanup`可以得到正確結果。

其他在coding style的必讀文件中，還會提到的規則諸如：function宣告，{}\(\)是否換行的用法，變數四則運算符號該怎麼擺，這些都是再基本不過的，在此就不另外贅述。

## checkpatch.pl

checkpatch.pl這個工具之後會反覆的用到。常用的模式有兩種。

* 檢查原始檔：./scripts/checkpatch.pl --no-tree -f XXXX.c \| less

  * --no-tree 表示不要參照source tree
  * -f 表示為檔案模式

* 檢查patch檔：./scripts/checkpatch.pl XXXX.patch

Error表示為必修的問題，Warning表示最好要修掉。但是某些以不這樣寫，人類就無法容易閱讀該段程式的狀況下，可以允許例外。不應該為了一點小小的效能差距，犧牲程式碼可讀性。

* 自動修正功能 ./script/checkpatch.pl --fix XXXX.c，這個指令會建出XXXX.c.EXPERIMENTAL-checkpatch-fixes的檔案，不會直接改到原始碼。將--fix換成--fix-inplace，則會直接對原始檔覆蓋修正。**注意：自動修復功能經常發生不預期的錯誤！**所以請小心使用。甚至使用後都應該再以人工，和重新編譯檢查過一次。這也是為什麼自動修正被標示成**EXPERIMENTAL**的功能。

## 其他輔助排版工具

除了u-boot和Linux內建的格式檢查工具，移植其他專案的程式碼過來的時候，也有通用型的排版工具可以使用。但是輔助排版工具跟checkpatch.pl的自動修正功能一樣，經常出錯，甚至有機會搞爛原本的檔案。而且coding style是會隨時間改進的，這些輔助排版工具所用的參數未必能永遠符合最新版的coding style。

### GNU indent

如果找不到indent指令，請安裝indent。

sudo apt install indent

使用手冊：[https://www.gnu.org/software/indent/manual/](https://www.gnu.org/software/indent/manual/)

常用範例：[https://www.gnu.org/software/indent/manual/html\_node/indent\_4.html](https://www.gnu.org/software/indent/manual/html_node/indent_4.html)

通常移植模組或演算法到其他軟體/平台上的時候， indent是你的好幫手，不過這個工具可以調整的參數實在是太多了。常用範例就列出自動排版為Linux kernel可以使用哪些參數。

-nbad -bap -nbc -bbo -hnl -br -brs -c33 -cd33 -ncdb -ce -ci4

-cli0 -d0 -di1 -nfc1 -i8 -ip0 -l80 -lp -npcs -nprs -npsl -sai

-saf -saw -ncs -nsc -sob -nfca -cp33 -ss -ts8 -il1

真的是誇張得多對吧！所以完整的指令就會長這樣：

indent  -nbad -bap -nbc -bbo -hnl -br -brs -c33 -cd33 -ncdb -ce -ci4 -cli0 -d0 -di1 -nfc1 -i8 -ip0 -l80 -lp -npcs -nprs -npsl -sai -saf -saw -ncs -nsc -sob -nfca -cp33 -ss -ts8 -il1 XXXX.c

然後一個不幸的消息是，由於範例文件年代久遠，部分參數可能會不符合現行的Linux coding style了。所以最後還是得用checkpatch.pl再檢查確認一次，再用人工修正。

