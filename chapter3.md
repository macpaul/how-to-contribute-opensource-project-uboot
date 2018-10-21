# 開發環境的準備

雖然u-boot也支援x86，但是大部分可取得的開發板，以及大家常用的開發平台，仍然是嵌入式的環境。因此最好還是準備一個給嵌入式環境的開發環境會比較好。包含Host PC，以及TARGET Device。

## HOST PC

通常這個HOST PC會需要一些硬體裝置，Ethernet port\(用於tftpboot刷機\)，Serial port \(COM\)\(用於UART指令操作\)。

如果你可以專門為PC安裝一個獨立的開發環境，譬如Linux作業系統，同時用於修改原始碼，編譯u-boot，並且刷機然後除錯，這樣當然很好。如果不行，我們也可以使用虛擬機器\(virtual machine\)，在VM裡面的Linux做原始碼修改和編譯，再把u-boot image拷貝到主機上的Windows環境，再用Windows的tftpboot和Teraterm操作UART command刷機驗證。當然也有一些人用MacOS或者FreeBSD做開發，但是都還是離開不了需要準備編譯和燒錄環境。

有些PC現在不提供debug用的實體COM port，這個時候你就需要USB-RS232的這種COM port轉接線。

下面是一種常見的開發環境組合：

* HOST PC：OS為Windows作業系統
  \*\* UART: Teraterm: [https://ttssh2.osdn.jp/index.html.en](https://ttssh2.osdn.jp/index.html.en)
  \*\* tftpboot: tftpd32: [http://tftpd32.jounin.net/](http://tftpd32.jounin.net/) 
* Virtual machine：安裝ubuntu Linux OS.
  \*\* 需要安裝cross-compiler、git

## Target Device

u-boot編譯好之後，要在哪裡跑呢？通常你應該會有一塊開發板，有UART會導出console log和command line，你可以操作Linux OS或者檢視系統運作的log。有一些開發板或者產品，怕被使用者刷機，或者被對手進行逆向工程，因此出廠前就會拿掉UART port。如果你碰到這種開發板，可能就會需要自己把UART port焊上去，並且找出該產品UART port的VCC/TX/RX/GND四根pin的實體位置。如果你需要驗證硬體相關的功能，譬如驅動程式，網路，燈號，GPIO等等，你都會需要一張實體的開發板。

如果你沒有實體的開發板怎麼辦？u-boot也可以跑在模擬器上。常見的模擬器是QEMU，u-boot也支援某些CPU架構的模擬器。所以你也可以在HOST PC，或者VM中，安裝QEMU。如果你參與的開發工作是純軟體應用的部分，QEMU應該能適用大多數的場景。

## Debugger

除錯的部分，如果你有除錯器\(debugger\)當然很好。常見的除錯器是Trace32或CodeVisor。除了原始碼的邏輯錯誤，導致燒錄後無法開機，必須使用除錯器透過JTAG燒錄之外，燒錄刷機時，燒錯partition也是開發過程中常見的失誤；這些時候都得使用除錯器重新讓這塊開發板能重新正常運作。

除錯器除了能逐行執行程式碼，也可以迅速查看出錯時的狀況，譬如register值，memory中相關變數的值，以及Call stack等等。不然工程師通常只能靠UART打印的除錯訊息\(debug message\)來排除錯誤，其實既耗時又沒效率。

如果你沒有除錯器，那開發和燒錄就得小心得多了。所幸除了手殘之外，大多數的時候並不會那麼容易碰到問題需要用除錯器重燒。如果你是用QEMU進行開發驗證，那準備一下GDB這種軟體的除錯工具也是很有幫助的。

## 準備編譯環境

下面會以ARM QEMU的Target device為例，我們可以準備相對應的編譯環境。

1. Linux PC \(或者Linux VM\)，建議ubuntu或者debian。
2. 安裝版本管理系統 git  
   apt-get install git

3. 安裝cross-compiler  
   sudo apt-get install binutils gcc g++  
   sudo apt-get install gcc-arm-linux-gnueabi

4. 安裝qemu  
   sudo apt-get install qemu-system-arm

## 下載原始碼

由於u-boot實在是太多人用了，git hub、各實驗室幾乎都會放上他們自己的u-boot原始碼。由於我們的目標是要提交改動回到最源頭，提交任何修改，當然也要基於u-boot的主原始碼庫。u-boot除了主要的原始碼庫，各模組和各CPU的維護者，為了工作流程上的方便，也有各自維護的原始碼庫。最後都還是會整合到主要的branch，首先我們先下載主要的原始碼庫即可。

git clone git://git.denx.de/u-boot.git

## 編譯第一個u-boot映象檔

下載好u-boot原始碼之後，即可嘗試編譯第一個u-boot映象檔，順便確認我們的邊應環境都有安裝正確。

cd u-boot

cp ./configs/qemu\_arm\_defconfig .config

copy target device預設的設定檔

make menuconfig ==&gt; exit=&gt;Yes

由於u-boot一直有很多新的功能，如果不使用menuconfig直接套用，我們會需要手動逐項選擇是否要開啟這些新功能。用menuconfig直接套用預設存檔，然後離開。

make

開始編譯。

## 使用qemu執行u-boot

安裝qemu-system-arm

qemu-system-arm -M virt,highmem=off -bios u-boot.bin -nographic

就可以執行你剛才編譯好的u-boot.bin。按Ctrl-a,x 離開qemu。

## 附註

更新環境準備上上要注意的。由於u-boot最新版的source code \(arm\)必須用gcc-6.0以上的cross compiler。所以如果你的Linux環境 \(ex: ubuntu\)版本太舊，可能沒有直接支援這個版本。ubuntu可以透過在套件庫加上custom support的安裝來源，增加新版的gcc。不然你就得自己編譯cross compiler.

### ubuntu上增加新的套件庫來源的方式

sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa

sudo apt-get update

sudo apt-get install gcc-arm-embedded

如果ubuntu太舊，有可能會發現custom support的安裝來源也達不到gcc-6.0的版本。目前測試過，建議直接裝到ubuntu 18.04版本。這樣gcc就直接支援到7.0版。我這邊驗證過，目前u-boot上面的qemu\_arm project是可以編譯成功的。  
小記：如果你發現用這樣的環境還是不能正確編譯，請看一下error log是否誤用x86的gcc。

例：請記得在編譯前，於環境變數加上export CROSS\_COMPILE=arm-linux-gnueabi-

再下make指令開始編譯。

