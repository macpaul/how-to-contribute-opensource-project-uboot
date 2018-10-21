# 原始碼概觀

## 網路上的原始碼解說資料

在網路上可以找到很多u-boot相關的教材，也有一些零碎的資訊。但是不管怎樣，最好的方式是自己研讀原始碼。簡體中文的資訊非常多，不過大多是針對中國境內流通廣泛的開發板撰寫的。在slideshare上面比較能找到其他國家的資源，譬如美、日、歐等等其他在全球廣泛流通的開發板。解說教材不外乎使用說明、原始碼檔案擺放方式、開機流程這些。不過其實從資料夾名稱大概就能看出架構上的設計的概念。

## 原始碼擺放方式

在u-boot的原始碼中下tree可列出所有樹狀的檔案結構。不過這樣一次列出太多看不完。我們還是一層一層看好了。你也可以用tree 直接看某一個folder下面有哪些檔案。  
tree -L 1  
![](/webimage/CH4.ScreenShot.SourceTree_L1)

api: machine/arch independent API for external APPs  
arch: CPU \(architecture相關\)開機、系統初始化、CPU特有硬體功能等等。  
board: 開發板相關設定。  
cmd: 可透過uart操作的指令。  
common: 通用功能或函式庫，譬如記憶體管理、周邊裝置的標準功能。  
configs: 各產品\(一個開發板可以衍生出很多不同產品\)的設定檔。  
disk: 分割區相關的功能。  
doc: 說明文件。  
Documentation: devicetree的相關文件。  
drivers: 各種周邊裝置的驅動程式。  
dts: 各產品或者開發板的devicetree的設定檔。  
env: u-boot執行時環境變數載入和儲存等等的支援。  
examples: 範例應用程式，除了helloworld還有一些link scripts。  
fs: 檔案系統的支援。  
inculde: 各種include header集中地。  
lib: 各種library，有壓縮、演算法、CRC、image header等等支援。  
Licenses: 各種相關模組完整的的Licenses宣告集中處。  
net: 網路相關的功能。  
script: 各種輔助scripts集中處，譬如檢查格式用的checkpatch，Kconfig等等的生成scripts。  
test: 單元測試code。  
tools: 各種工具，譬如打包image header，gdb或者嵌入開機logo的支援。

MAINTAINERS: 這個檔案很重要，紀錄哪些產品、開發板、晶片、各模組相關的維護人員。如果你發現bug或者有patch要提交，都可以從這個檔案找到相對應的開發者。  
Makefile: 定義編譯時所需要的相關參數。已經需要編譯哪些folder的判斷，編譯前後什麼階段需要呼叫輔助程式來打包binary。

## 開機流程

開機流程真的很複雜，包含CPU如何初始化，u-boot怎樣把自己從emmc、eeprom、SD card等等儲存裝置讀出來，然後怎樣初始化記憶體，再把u-boot自己搬過去記憶體特定位置擺好。這部分通常都牽涉大量的組合語言，以及組合語言與C語言交錯呼叫，記憶體規劃方式。幾乎可以另外寫一本書了。而且就算是同一家的CPU產品，不同世代之間的開機流程也會，也會因為硬體設計不同，有很多差異。通常廣為使用的就是x86，arm/arm64，mips。台灣本身有NDS32這個CPU架構，以及最近比較熱門的新興架構：RISC-V。

u-boot和其他boot loader最不一樣的地方，就是u-boot會動態計算記憶體總共大小，然後把自己\(u-boot\)放到記憶體頂端去執行。是不能hard code直接放在某一個位置跑的。這樣對整個系統後續要載入的軟體，才有最大的彈性。這也是u-boot開機流程比較複雜的地方。這些在網路上都教材都很多，可以google一下就能找到不少資料。還是看你有興趣學習哪一種系統的組合語言，才知道你適合研究哪一種開機流程。

ARM的部分，可以先看一下 [http://albert-oma.blogspot.com/2016/07/embedded-u-boot.html](http://albert-oma.blogspot.com/2016/07/embedded-u-boot.html)對ARM開機流程，以及link script（Binary layout）有一個簡單的解說，可能稍微有感覺一點。然後再去深入的找哪個部分要對到什麼程式碼檔案，然後去找網路上的筆記或教學資料。

MIPS也有類似的說明 [http://blog.xuite.net/tzeng015/twblog/113272452-mips%E6%9E%B6%E6%A7%8Bu-boot+%E5%95%9F%E5%8B%95%E6%B5%81%E7%A8%8B](http://blog.xuite.net/tzeng015/twblog/113272452-mips架構u-boot+啟動流程)

以上在slideshare上也可以搜尋到大量的投影片包含圖解，會比較容易瞭解。

NDS32的部分，[https://www.slideshare.net/macpaul/bootstrap-process-of-u-boot ](https://www.slideshare.net/macpaul/bootstrap-process-of-u-boot)上有比較清楚的程式碼和圖解說明。影片的話可以參看[https://www.youtube.com/watch?v=08QQww1Yc1Q](https://www.youtube.com/watch?v=08QQww1Yc1Q)



