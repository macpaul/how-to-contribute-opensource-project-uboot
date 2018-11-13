# 如何製作品質合格的patch

## 再次強調：遵守coding style是提交patch最基本該做到的事情
一個專案如果能隨時間成長，使用的人變多，需求變多，開發人員也就會隨著增加。專案的社群成長茁壯，自然使用者回報的bug會變多，關於功能的討論變多了，同時在郵件論壇上提交的patch也會越來越多。有很多開發人員其實是利用個人業餘時間，進行開放原始碼專案的貢獻。大家能夠空出來進行開發的時間，可能都不是很多。為了讓空出來的時間被充分利用，很快的進入狀況搞懂大家在談什麼很重要。也因此，專案維持一致的coding style是最基本的需求；讓每一個參與者，都能很快讀懂每一筆patch，理解這段程式的功能和目的，然後給予建議，最後討論是否要採納。由此可知，為了讓討論的過程有效率，一旦接受新的patch，還要兼顧考量日後的維護和保有擴充的彈性，patch遵守專案的coding style是再基本不過了。

## 維護人員接受patch的流程
不論提交到論壇的patch是新的功能，或是bug fix；也不論patch大小修改多寡，全部都需要經過維護人員的閱讀審視(review)檢查是否有錯誤，（同時在重複審視的時候，社群就會再次檢查是否符合coding style）。一旦patch被接受，維護人員會嘗試將patch合併至原本的程式碼中（通常使用git am指令），看是否能順利合併。假使這些patch都能順利合併，還需要透過編譯測試、甚至安裝到測試環境上（對u-boot來說，就是將編譯出來的程式，燒錄到開發板上）執行看看。一切都驗證無誤之後，才會正式公告這個patch被接受了。並且透過git push合併到主線（master branch）或是合併（git merge）到下次release（next branch）之中。

有的時候提交的patch不會只修改一個檔案（譬如.h檔裡面的#define需要修改或新增宣告，那.c檔的程式通常也會跟著一併修改或新增功能），更多時候社群會收到開發者提交一整組的patch，我們通常稱為patch set。審核過程如果社群有人有意見，提出修改的要求的時候，就算只修改了一個地方，還是得整組patch set進版本號然後重送。如果這組patch之間相依性（dependancy）切得很乾淨，也是有機會部分重送就好，不過這種情況很少。而且如果patch之間相依性很低，那就乾脆拆成好幾筆無關的patch set分開提交就好了，不用一次全部打包在同一個patch set。因此製作品質合格，結構良好，邏輯清楚的patch是很重要的，不但有助於減少提交patch的開發者自身修改patch的時間。對專案來說，接受這些patch之後，萬一產生bug，要追查、退版本、安排人力進行bug fix，也會相對得有效率得多。

## 如何製作好的patch

其實跟coding style一樣，不少專案的說明文件都會附帶製作好的patch的準則。好的patch（或是patch set）不外乎幾個原則：
* 按照寫code的先後邏輯順序組成patch
* patch應該要易於分割重組，力求簡單明瞭
* 相關的patch放一起，無關的patch下次再提交
* patch必須盡可能不要影響其他模組或功能。
* 如果修改到某個通用的介面，要一起修改其他有用到這個介面的程式
* patch不應該造成其他模組編譯錯誤，因此送出前最好都要先在local環境確認編譯和執行正確

例如u-boot的wiki上，就有很詳盡的patch製作準則說明：[https://www.denx.de/wiki/U-Boot/Patches](https://www.denx.de/wiki/U-Boot/Patches)，這些準則，通常也適用於Linux和其他的Open Source專案。u-boot的這個wiki頁面，也提到提交Linux的Patch準則：[https://lwn.net/Articles/139918/](https://lwn.net/Articles/139918/)。（必讀）你看，共通概念通常不會差很多，雖然Linux這篇守則完成時間比較早，但還是建議一定要讀過一次，所以我們這邊就不針對Linux這篇守則另外說明。以下針對提交u-boot的patch的準則，挑重點逐條解說。

## 提交u-boot patch的準則

* Release early, release often. 
>* 這也是所有open source專案的最重要的準則之一。盡可能早一點release（新版本），盡可能頻繁的release。這樣新功能和新的bug fix才能越快讓使用者受益；如果有新的bug也才能及早發現，及早處理。這個準則對於送patch到專案也是一樣的。有的時候你的patch被拒絕不是因為他不好，可能只是需要跟大家討論，然後需要很多的修改以符合大家的需求。甚至在送出[**PATCH**]之前，就先把你的構想在郵件論壇上先找人討論，會有助於之後你送patch的工作。提出修改的構想，請論壇上的朋友給予意見，這種形式的作法我們通常叫他：[**RFC**]，**Request for Comment**。之後你就可以根據郵件論壇上大家討論的想法，來形成你要提交的patch的架構，以及組織撰寫的功能和流程。

* 所有的patch都必須送到郵件論壇
>* 如果你的patch跟某些有固定的維護人員的檔案相關，請在郵件副本加上他們。為了防止後續版本的patch會忘記加上他們到CC:，你也可以在patch的commit message中的簽章之後，加上Cc:。
>* 記得加上有回覆你意見的其他人到Cc:，或者前面跟你改過同一個檔案的其他人。他們也會幫忙檢查後續版本的patch是否能符合他們的期待；或者你後來有更好的修改的方法！

* Patches的修改必須是一個完整的邏輯
>* 不同且無關的修改必須分開成不同的patch set送出。一個程式功能的修改，只能送出一組patch set。
>* 如果一組邏輯的修正包含或者增加了新的檔案，那這些修改必須包在**一個patch**中送出。
>> 上面這兩段白話就是：譬如你寫了兩個個功能，那一個功能就只能包在一組patch set裡面。第二個功能要分開處理。如果有一個功能，新增了一個.h檔，兩個新的.c檔，修改兩個舊的.c檔；那這五個檔案都必須被包在同一個patch裡面送出。這樣可以維持邏輯上的先後順序，並且避免每一筆patch之間的編譯失敗。

* 非功能上的改變，例如Coding style的修正，不要跟bug fix一起提交。並請加註：**cosmetic**。把這兩者分開對整個review過程大大的有幫助。不過你提交的bug fix仍然得符合coding style。

* 使用checkpatch.pl的幾點建議（這邊只摘錄部分，原始內容還是請自己閱讀）
>* Checkpatch不是完美的，所以請用common sense來判斷他回報的結果。
>* 如果patch的一部份被報出問題，但是不是這次所patch修改到的部分，而是已經存在的其他舊程式碼。請先送出coding style的修正，同時記得要標註**cosmetic**。

* **用純文字送出你的patch**。禁止HTML、MIME、連結、壓縮、夾檔。社群只收純文字。
>> 最好的方法是用"git format-patch"指令。
Patch請用"master" branch為參照基礎，除非你要送的patch是針對release cycle中的"next"。通常是release前測試版發現的bug修正，或是緊急到非得趕上release的特殊功能。

* 確定你的電子郵件軟體不會亂改你的patch，譬如自動轉換tab和空白，或者自動斷行。因此最好是用"**git send-email**"或是"**git imap-send**"指令。

* Patch的主旨請用確實明確的描述填寫，然後不要超過60字元之類的。

* 主旨開頭必須要用有意義的tag，這個tag通常是module name或者是功能。

* 主旨必須帶**[PATCH]**字串，可以用**git format-patch**自動產生。

* 如果你送出一組patch set，請確保主旨和標題有描述patch的順序和總數目。（**git format-pach -n**）。如果這組patch set比較複雜也比較多，你可以用（**git format-pach -n --cover-letter），這會多加一筆針對這份patch set的摘要說明的**前言patch**（第0筆patch）。

* Patch的內容部分
>* Bug fix：要寫上原來是什麼bug，有什麼現象，或者如何重現這個bug。然後說明你如何修正這個問題。
>* New feature（新功能）：請說明這是什麼功能，然後你怎麼實做的。

* 郵件論壇的郵件大小上限為100kB，如果一個patch的大小超過x了，你就應該拆分你的patch成幾個不同的邏輯構成的patch set。如果你的patch在未壓縮的狀況下超過這個大小，而且又絕對無法分割。這個時候才能使用URL的方式提交你的patch。請放在一個至少可以存在很長的時間的位置；用"pastebin"（一種貼上程式碼的網站）是不夠的。你可以把太大的patch放到u-boot的wiki[TooBigPatches](https://www.denx.de/wiki/U-Boot/TooBigPatches)這裡。

## 其他請務必自行閱讀的重要守則

其他在U-boot的[https://www.denx.de/wiki/U-Boot/Patches](https://www.denx.de/wiki/U-Boot/Patches) wiki頁面上提到的重要守則還很多，就請認真閱讀。再列出幾點特別注意的項目如下。

* 標註程式屬性、版權宣告、簽章： [Attributing_Code_Copyrights_Sign](https://www.denx.de/wiki/view/U-Boot/Patches#Attributing_Code_Copyrights_Sign)
>* 最重要的是送patch一定要記得用**git commit -s**簽章"**Signed-off-by:**"。
>* 如果你有重大的改動，大到足以在這個檔案宣告所有權時，或是你從頭寫的一份新的檔案，才能在檔案的開頭宣告**GPLv2+ SPDX-License-Identifier**以及你個人所屬的Copyright。
>* 如果你從其他專案移植或者copy檔案到u-boot，請明確說明從哪裡Copy過來的，最好甚至提供copy來的**git commit ID**

* Commit message的慣例： [Commit message conventions](https://www.denx.de/wiki/view/U-Boot/Patches#Sending_updated_patch_versions)
>* 這邊比較瑣碎，但是讀一遍吧，加深印象只有益處。

* 提交新版patch： [Sending updated patch versions](https://www.denx.de/wiki/view/U-Boot/Patches#Sending_updated_patch_versions)
>* 重送patch時，務必標上版本如"**[PATCH v2]**，可以用**git format-patch --subject-prefix="PATCH v2"**自動補上。
>* 在git commit簽章後到檔案列表中間，請務必手動補上每一筆patch的**ChangeLog**（修改的紀錄），由於cover letter並不會在維護人員管理patch的[Patchwork](http://patchwork.ozlabs.org/project/uboot/list/)中出現，就算你在cover-letter總結每一筆patch都在做什麼，你仍然需要在每一個版本的patch都補上ChangeLog。這邊請看u-boot wiki上的範例。
>* 為了維護郵件論壇上討論的品質，並且避免社群漏掉你某一版的patch，務必填上正確的"**In-reply-to:**"和"**References:**"的e-mail header。這樣patch跟社群給建議的討論串，才能用正確的順序方式呈現。也才會讓大家知道你這筆修正是針對哪個討論的部分送出的，並且維護正確的討論串的結構。

* 務必檢查**master branch**能打上你的patch set，並且可以用**MAKEALL** script編譯所有的project，而沒有產生編譯warning和錯誤。
>* 同時建議使用out-of-tree編譯（用"-o" make option，以及"BUILD_DIR"環境變數）。例如：
BUILD_DIR=/tmp/u-boot-build ./MAKEALL
>* 至少針對你的平台的architecture下所有的project都用MAKEALL編譯過一次。