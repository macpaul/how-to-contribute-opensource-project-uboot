# 第一次貢獻：清理程式排版

看到這裡，我們假設你對git操作有一定的瞭解、對u-boot專案的程式碼排版風格也有大略認識，也已經加入郵件論壇，收到雪片般的信件，但是都不知道他們在討論什麼。沒關係，有以上基礎，應該可以開始提交最簡單的貢獻了。邁出第一步其實不會太難，但是也有點門檻。首先，我們需要找到比較舊的，通常是很早期就提交到專案裡的程式碼，這些程式碼通常不符合現在整個專案的程式碼規範。當時專案沒有太多人參加，架構也不一定規劃得很好，一切都才剛起步，因此可能沒有形成很好的社群規範。但不是說這些老舊的程式就沒用，相反的，他們可能扮演整個專案一些重要的角色，但是因為實在太久也太穩定了（或是太基礎），導致沒有人注意到應該花時間去翻修他們。但這些舊程式碼（Legacy code）就成了新手入門很好的練習題。

除了比較早期就存在的程式碼，u-boot跟Linux有一點不太一樣的地方是，u-boot有一些功能，一些模組，是從其他專案移植過來的。這些模組，有些是某大學某實驗室的研究論文，有些是從Linux移植過來，有些可能是某實驗性質的專案的某個小功能。因此他們的程式碼排版風格都不盡相同。重點是這些移植過來的程式碼，都與GPLv2相容，才能被u-boot納入使用。這些排版風格跟u-boot不一樣的程式碼，就最好保持他們原本的格式，不需要更動。這些模組日後升級新的版本時，才能避免格式上大幅度的修改，也方便開發者去比較哪些地方存在新舊版本的差異。

（對Linux來說，Linux雖然也移植其他開放原始碼專案的功能，但是都按照Linux自己的架構有所演化，而且可能也被翻修過是符合Linux排版風格的。所以在某個folder下的程式碼風格跟其他人不一樣的部分就非常少見。）

## 提交patch的原則

在我們開始搜尋哪些程式碼可以被改進之前，我們需要先瞭解提交patch的幾個原則。不管參與哪一種專案的開發，下面的幾個原則應該都是共通的。

1. 不可以造成其他功能或者其他子專案編譯失敗（build fail）或者編譯錯誤（build error），在提交之前最好把會用到的相關模組都編譯過一次，確保不會編譯失敗。
2. 如果提交的patch包含API的更動，那你必須幫其他也用到這個API 的模組一起更新。譬如你對write\_buffer API增加了一個新的參數，那所有其他使用這個API的模組，也都必須要支援這個新參數的設定。同樣不能造成其他模組的編譯失敗。
3. 提交patch需要按照編譯上的邏輯順序，同一個功能的.h和.c檔最好一起提交。譬如有usb.h和usb.c兩個**有相依性**的檔案要同時提交，最好兩個檔案一起提交，頂多可以拆成usb.h先提交，再提交usb.c。切記不可以先提交usb.c再提交usb.h。除了可能會有邏輯問題之外，也可能會造成編譯錯誤（build error）。
4. 不可以將好幾個不同的功能，擠在同一筆patch提交。應該要將這些功能盡可能拆分成獨立的幾筆patch，就算少了某一兩個功能的patch，其他的patch也要能夠獨立編譯，獨立執行。
5. patch提交前，最好使用checkpatch.pl檢查過，並且移除patch再重新打上，檢查是否有行尾或者換行空白需要再修正的。
6. patch提交前，最好都先用git pull更新原始碼到最新版。所有新提交的patch，都必須以最新版的codebase為基準，以免你的patch跟其他人的修改造成衝突，甚至可能會讓你做白工。另一方面也讓reviewer能夠直接在他的git codebase打上你的patch做驗證。

## 尋找格式需要改善的程式碼

在第五章提過，我們可以使用 ./scripts/checkpatch.pl --no-tree -f XXX.c \| less直接檢查某個檔案是否有排版格式上的問題。譬如我們可以直接對cmd folder下的檔案做檢查。 ./scripts/checkpatch.pl --no-tree -f ./cmd/\* \| less

![](/assets/ch8_checkpatch_cmd_01.png)

我們可以看到checkpatch指出./cmd/adc.c沒有格式問題。但是下一個檔案./cmd/aes.c就有格式問題，他同時也列出問題出在哪些地方。下面還有很多檔案，我們先不急著瀏覽，在這裡，你可以先按"q"退出less。

我們用tig來看一下./cmd/aes.c的歷史紀錄，如果你的系統不認得tig，還請sudo apt install tig裝一下這個git log瀏覽器。tig ./cmd/aes.c. 你可以用上下左右鍵和Enter來瀏覽針對./cmd/aes.c的改動記錄，按'q'就可以離開tig。

![](/assets/ch8_tig_cmd_aes.c_01.png)

可以看到./cmd/aes.c總共有五筆紀錄，看起來這個檔案在2016年應該就存在了，進去看他的git log，可以發現這個檔案之前檔名是./cmd/cmd\_aes.c，因為統一移除cmd\_，在git裡這個檔案看起來才是新的檔案。第二筆資料是2017年，這筆看得出來他同時有另外一筆提交的檔案，是更改檔名aes.h為uboot\_aes.h，同理為了避免build error，必須在./cmd/aes.c的include file修正uboot\_aes.h的宣告。另外最新的兩筆修正看起來是為了移除無用的宣告，以及符合SPDX 授權宣告GPL-2.0+的改動。到這裡，不管這個aes.h, aes.c之前是從哪裡移植過來的（很有可能是Linux），但是看起來應該也演化成u-boot獨有的版本了。因此我們應該可以對checkpatch.pl指出的問題進行修正。**注意：一開始練習時請不要使用自動修正功能，自動修正也可能把正確的更改成錯的。**

## 動手修正checkpatch指出的錯誤

在這個範例裡，我們只需要把28,29,30,31行的資料型別，從uintXX\_t換為建議的uXX就可以了。至於第88行，檢查原始碼，我們會發現U\_BOOT\_CMD其實是一個 MACRO，定義在include/command.h，相當於一個function。由於壓縮成一行也不會超過每行80字元規定，所以這裡可以把他改成一行U\_BOOT\_CMD\(aes, 7, 1, do\_aes, "AES 128 CBC encryption", aes\_help\_text\);；或是留下第一個參數跟 U\_BOOT\_CMD\(同一行：U\_BOOT\_CMD\(aes。修正完畢之後我們就可以存檔離開，並且用git diff回顧一下之前的改動有沒有錯誤的地方。

![](/assets/ch8_git_diff_cmd_aes_01.png)

於是我們再用checkpatch檢查一次，發現還是得到error: CHECK: Alignment should match open parenthesis.7的參數必須跟aes參數對齊。所以我們只好再修改一次。

![](/assets/ch8_git_diff_cmd_aes_02.png)

這次用checkpatch檢查./scripts/checkpatch.pl --no-tree -f ./cmd/aes.c \| less我們就得到完美的結果了。

total: 0 errors, 0 warnings, 0 checks, 92 lines checked

./cmd/aes.c has no obvious style problems and is ready for submission.

相較之下，直接寫成一行是否比較省事呢？其實也未必，在兼具可讀性上，的確是各有取捨的地方。

修正完畢，記得馬上用git add ./cmd/aes.c把成果存起來。然後用git commit -s提交改動到local的git repository。

![](/assets/ch8_git_commit_s_cmd_aes_01.png)

標題Subject:必須指出是哪一個模組（cmd）下的哪一個功能或檔案（aes.c）的改動，說明改動理由：fix coding style。空行後大略描述改了哪些東西：data type，和其他coding style修正。這邊最好註明coding style修正是根據checkpatch.pl，因為checkpatch.pl經常更新進版，但是會review你的patch的人不一定知道最新版的checkpatch新增了哪些規則。git commit的-s參數代表要用你的email做簽章。存檔離開之後，就可以在tig中看到我們修正已經被合入local的git repository。

![](/assets/ch8_tig_cmd_aes_02.png)

## 驗證是否能正確編譯

有的時候這些coding style錯誤，可能是可以直接被提交出去，並不會造成其他相關模組build fail。但是這個範例中，涉及宣告型別的轉換uintXX\_t跟uXX不同，就得看include的header裡面是否有用引入新的型別宣告，否則一定會造成build error。那到底什麼專案，或是什麼狀況下，才會編譯這個./cmd/aes.c呢？我們可以查看./cmd/Makefile得知。

![](/assets/ch8_cmd_makefile_01.png)

只要project的配置中有打開CONFIG\_CMD\_AES，編譯的時候就會編譯到aes.c。由於我們先前已經編譯過project: qemu\_arm （設定檔：qemu\_arm\_defconfig）我們只要在.config打開CONFIG\_CMD\_AES的選項就可以了。更改\#CONFIG\_CMD\_AES is not set為 CONFIG\_CMD\_AES=y

![](/assets/ch8_enable_cmd_aes_in_config_01.png)

好的很幸運的，我們編譯成功了。接下來就可以提交patch到u-boot mailing list

## 提交第一個patch

由於我們這邊只修正了一個檔案，所以只要提交一筆patch就可以了，相對簡單。之後會提到如果有連續幾筆而且有相依性的patch該如何提交。由於之前的修正已經存入我們本地的git repository中。我們現在只需要取出成檔案，並且寄信到mailing list即可。由於我們的修改存在最尾端，所以只要跟git reposity取出最後一筆，並且以format-patch形式取出。

使用git format-patch HEAD~1指令，按下enter後，patch就會被存成名為0001-cmd-aes.c-fix-coding-style-according-to-checkpatch.p.patch的檔案。由於我們可能會一次提交許多檔案，git會自動幫你打上編號。我們可以檢視一下這個patch的內容。要從頭取出幾筆patch就在HEAD~?寫上筆數即可。

![](/assets/ch8_format_patch_cmd_aes_01.png)

這邊可以注意到原本的subject，被format-patch指令加上了\[PATCH\]標籤，這在郵件論壇和收信軟體裡有助於分類。如果你的patch沒有第一次就被接受，可能需要手動維護patch版本。由於\[PATCH\]這個標籤，在合入reviewer的git repository的時候會被git am 指令拿掉，一旦你需要提交第二版，或是第三版的patch，就會需要手動更改\[PATCH\]標籤的版本，像是\[PATCH v2\]。

## 寄出patch到郵件論壇

再次確認你的patch沒有問題之後，就可以使用git send-email寄出patch到郵件論壇，寄出patch的時候請按照順序依次指定patch的檔名。像是git send-email 0001-XXX.patch 0002-XXX.patch...等等。在這個範例我們只有一個patch，指令為：git send-email 0001-cmd-aes.c-fix-coding-style-according-to-checkpatch.p.patch![](/assets/ch8_git_send_email_01.png)git send-email會詢問你下述問題

* 誰提交這份patch？通常就是我們
* patch要email給誰？一定要填上u-boot@lists.denx.de郵件論壇的地址，另外如果這個檔案有固定的維護者（maintainer），最好也加上他，以免他漏掉你的patch，也可以加快review的速度；也可以加上其他常對這個檔案或模組有貢獻的人。這些訊息除了從MAINTAINERS檔案中可以查詢，也可以透過tig或者git log確認。
* Message-ID to be used as In-Reply-To for the fist email？這個在CH7有提到，Message-ID用來建立同一個Thread討論串的相依性。第一次提交patch的時候通常可以不用填，但是如果有第二版、第三版的修正，最好補上正確的Message-ID，讓大家知道接下來的patch跟先前的某一個patch有相關，是處理同一個議題的。
* Send this email？\[y\]es就送出，\[a\]ll如果不只一筆patch，可以選a，讓後面的patch（同一個模組有相依性的修正）都沿用同樣的email設定。
* Password: 這邊跳出的password是登入mail server的密碼，如果你的mail server是gmail，在CH6有提到，必須使用Google應用程式密碼；而不是登入gmail的那組密碼。

以上步驟都完成之後，本地的電腦就會嘗試寄出patch到u-boot郵件論壇。如果成功了，你應該會收到一份自己寄出的patch的副本，幾個小時之後，在郵件論壇的archieve上應該也會看到你提交的patch，接下來就是等待其他人review你的patch，然後看是否要接受你的貢獻。這通常會花上幾天甚至幾個星期的來回討論。

## 善用branch建立開發用branch，並且保持master的更新

如果你的patch被接受了，恭喜！你成功的提交了第一筆patch，可以著手進行更進一步（接下來）的貢獻。在程式開發，製作patch的過程中，上游的git repository經常會更新。如果你的local存有其他自己的改動，在更新codebase到最新版的時候，會發生git merge。這個通常是維護者比較不希望看到的git log，一方面因為你還沒完成的patch會不好取出，另外一方面是產生的merge log比較不好閱讀和追蹤來源。所以我們會建議你開一個work or temp branch，所有的改動都在work or temp branch上進行。留著master主線跟隨社群最新版的codebase。

git reset --hard HEAD~1 由於剛才的改動已經存成file了，所以我們退掉local在master上的patch。

git branch work 建立名為work的branch  
git checkout work 切換工作環境到 work branch上  
git am  0001\_XXXXXXX.patch你剛才的改動

要更新master主線的code到最新版，只需要切換回master bracnh。然後再git pull取回最新的codebase即可。

git checkout master  
git pull

同樣的，有了主線的更新，work branch也可以用rebase的方式更新codebase，使用rebase指令，也可以避免產生merge log。

git checkout work  
git rebase master

這樣就可以同時保有master作為基礎的codebase參照，並且同步更新work branch中的codebase，保持開發的彈性。

