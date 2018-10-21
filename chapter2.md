# u-boot官網

以下介紹官網首頁提供各連結說明

官網wiki網址：[https://www.denx.de/wiki/U-Boot](https://www.denx.de/wiki/U-Boot)

![](/webimage/CH2.Screenshot.u-boot.web.mainpage.png)

## Documentation 文件總集

[https://www.denx.de/wiki/U-Boot/Documentation](https://www.denx.de/wiki/U-Boot/Documentation)

官網上的文件雖然有些年代久遠，但是資料相當齊全。除了提供使用者，u-boot支援的指令說明，也提供安裝方式，應用程式的開發範例，移植新平台的說明以及開機流程，錯誤排除等等的經驗知識。以下列出必讀的文件。

### Manual 使用手冊

[https://www.denx.de/wiki/DULG/Manual](https://www.denx.de/wiki/DULG/Manual)

必讀！

使用手冊包含開發環境的安裝介紹，開發環境的軟硬體準備和設定，各種操作指令的使用說明。到最重要的Flash分割區的擺放方式，如何載入Linux開機映象檔\(Image\)，該選用怎樣的分割區檔案格式，都有非常詳細的介紹。

### FAQ 常見問題

[https://www.denx.de/wiki/DULG/Faq](https://www.denx.de/wiki/DULG/Faq)

常見問題當然蒐集了郵件論壇，以及其他網路論壇上常見的月經文。大部分是使用上的故障排除。其餘大多是開發上會碰到的問題。不論是哪一種問題，通常都是因為工程師對軟硬體系統的不瞭解。FAQ中提到的觀念，對於工程師在嵌入式專案或者產品的開發上，包含系統的設計，都很有幫助。

### 設計原則

[https://www.denx.de/wiki/U-Boot/DesignPrinciples](https://www.denx.de/wiki/U-Boot/DesignPrinciples)

必讀！

這邊列出u-boot的十個黃金設計守則。不管你想開發什麼樣的系統，都建議閱讀參考一下。跟大型作業系統用途不一樣，u-boot定位為boot loader，所以保持輕巧快速是必要的。當然實務上，boot loader上面通常也會需要支援產品量產，或者是系統除錯等等功能，都不應該太過複雜，必須遵守u-boot的這十大設計原則。

由於u-boot同時支援眾多的平台，當然這十大設計原則也非常注重可移植性，以及原始碼必須可重複開發利用。所以在程式碼風格\(Coding style\)的部分，也有相當嚴格的規則，請參見 [https://www.denx.de/wiki/U-Boot/CodingStyle](https://www.denx.de/wiki/U-Boot/CodingStyle)。這邊與Linux可能會稍微有些出入，之後會有獨立的一章來介紹Coding style，以及相關的輔助工具。

