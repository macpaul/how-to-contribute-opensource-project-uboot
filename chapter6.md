# Git、編輯器、郵件設定等等

讀到這裡你也許會覺得很奇怪，之前開發環境的準備已經做了這麼多步驟了，為什麼還有一堆其他的工具需要設定？如果你沒有跳過CH5程式排版風格（Coding style），就會發現，要提交改動，還有好多額外檢查工作要做。當然我們是工程師！必須善用工具簡化這些流程，讓我們專注在撰寫程式碼和除錯上面。以下會逐步介紹必用的工具和設定，節省你改code看code解bug的時間。

## Git

首先，每一筆提交的patch都必須要簽名以示負責。你應該不會覺得每次完成了一個patch就要手簽一次很酷吧！你可以用下面的設定一次設定完畢。

git config --global user.email "xxxxyyy@gmail.com"  
git config --global user.name "XXXX YYY"

你剛才看到了吧，需要**簽名**負責，確保這個patch不會遭到propirety software的污染。以及如果有惡意程式碼，我們也可以找得到人負責。更重要的是，如果有人發現你的patch有bug，幫你改善，可以透過你之前簽名的紀錄，找到你幫忙review他的patch。這個設定會在未來提交patch的時候透過 git commit -s \(--signoff\)自動加上簽名。之後在git log就會看到 Signed-off-by: "XXXX YYY &lt;xxxxyyy@gmail.com&gt;"。在上面兩個指令之後，你會在家目錄的~/.gitconfig看到

\[user\]  
name = XXXX YYY  
email = xxxxyyy@gmail.com

為了方便和社群其他人的開發順利，請務必使用「**每天都會看信的信箱**」，做為簽章email。**社群的人對你的patch有什麼意見，你才會第一時間看到。才不會發生拖到幾天甚至十幾天之後才看到，屆時最新版的source code已經被其他patch改變，造成你的patch無法合入，得重新製作patch，整個review流程必須重來的狀況。**

以下使用gmail帳號為範例，解說git還需要做哪些設定。其實你可以直接編輯設定檔 vim ~/.gitconfig加入以下資訊。\(git book似乎無法縮排，請自行縮排\)\[sendmail\] 的部分是為了你可以直接使用git send-email XXXX.patch 到郵件論壇。**注意：千萬不要複製貼上你的patch到gmail或者其他郵件軟體中才寄出，會造成排版跑掉，甚至會有其他非預期內的問題。**\[color\]的部分是Ch5就已經設定過的，在git diff輸出可以提醒不合法空白字元。

\[sendmail\]  
smtpuser = xxxxyyy@gmail.com  
smtpencryption = tls  
smtpserver = smtp.gmail.com  
sslVerify = false  
\[color\]  
ui = auto

如果你習慣用vim作為預設編輯器，請加上：  
\[core\]  
editor = vim

emacs的user也請加上editor = emacs。

## 編輯器

由於我只會用vim，以下只解說vim的設定。如果沒有安裝vim請先安裝vim。

sudo apt install vim。

vim ~/.vimrc  
syntax on  
color desert  
highlight OverLength ctermbg=red ctermfg=white guibg=\#592929  
match OverLength /\%81v.+/  
au BufReadPost \* if line\("'\""\) &gt; 0\|if line\("'\""\) &lt;= line\("$"\)\|exe\("norm '\""\)\|else\|exe "norm $"\|endif\|endif

syntax on: 支援程式語言辨識的上色功能  
color desert \(或者是colorscheme dessert\): ubuntu預設的某些顏色會用深藍色，當終端機\(putty\)一類的設定背景色為黑色的時候就很容易看不清楚。範例用的desert是你也可以換成其他的配色。  
highlight OverLength XXXX match OverLength XXXX 對於一行超過80字的部分要標紅色highlight。由於傳統CRT螢幕一行只能有80字，到目前還是有工程師用傳統CRT螢幕再寫code。人類一次閱讀一行程式碼的極限大概也差不多是80字，所以必須遵守每行80字不能超過。  
au BufReadPost XXXX: 重新打開這個檔案，編輯器會回到上次停下來的地方。

其實編輯器還有很多其他設定可以輔助開發，譬如tags模組可以像SourceInsight那樣trace code等等的。但是跟 u-boot/Linux相關最重要的應該是這幾個設定。

## 郵件設定

有些人是公司贊助（任職）參與開放原始碼專案開發的，這個時候就會希望用公司email，已累積公司的貢獻度或知名度。但也有不少獨立開發者，是用私人email或者免費email帳號，如gmail參與開發和郵件論壇的討論。我們在前面git設定也看到，如果要用gmail帳號提交patch，需要加入SSL相關的設定。以下會解說gmail這邊要如何才能設定正確，讓你能使用git --send-mail XXXX.patch提交改動。

### gmail使用應用程式密碼登入

gmail在沒有開通「[使用應用程式密碼登入](https://support.google.com/mail/answer/185833?hl=zh-Hant)」之前，是不能用其他的應用程式寄信的。詳細說明可參看[https://support.google.com/mail/answer/185833?hl=zh-Hant。也就是說，使用git或者其他非電子郵件程式寄出patch時，需要輸入一組替代性的16位數通行碼，取代gmail的密碼。要產生這組密碼，請登入「\[應用程式密碼\]\(https://security.google.com/settings/security/apppasswords\)」頁面。](https://support.google.com/mail/answer/185833?hl=zh-Hant。也就是說，使用git或者其他非電子郵件程式寄出patch時，需要輸入一組替代性的16位數通行碼，取代gmail的密碼。要產生這組密碼，請登入「[應用程式密碼]%28https://security.google.com/settings/security/apppasswords%29」頁面。)

![](/webimage/CH6.ScreenShot.gmail-application-password.png)

![](/webimage/CH6.ScreenShot.gmail-application-password-gen.png)

由於16位數的密碼真的很難記下來，而且我們也不會每天都用gmail送patch，建議還是先找各安全的地方抄下來。之後只要用git --sned-mail 送出patch的時候，就會用到這組密碼。

## 搜尋工具

ubuntu上最常使用的搜尋工具是grep，查code或者字串的時候，grep -nrH經常用來做暴力搜尋。不過grep會檢查.git自帶資料庫中的資料，會多出很多工程師無法直接使用的結果。這邊另外要推薦的是silver-searcher ag，他的平均搜尋速度會比grep快，也會跳過.git資料庫的內容。這個是要另外安裝的。

sudo apt-get install silversearcher-ag

通常用法是ag XXXX字串 YYYY檔案。如果你還是習慣看grep的輸出結果，ag --noheading差不多跟grep -nrH等效。你可以在.bashrc中加上  
alias agnoh="ag --noheading"  
再讀入.bashrc設定檔  
source ~/.bashrc  
之後就可以使用agnoh取代ag --noheading這一長串指令了。

## 多終端機畫面編輯byobu \(screen\)

byobu（或者screen）可以讓你在同一個終端機畫面中，切出很多虛擬終端機（window），方便你同時進行改code，編譯，除錯等等工作。byobu預設已經會進入多虛擬終端機模式，你只要按下ctrl-a, c，就能新增第二個虛擬終端機視窗。首次使用，byobu會問你要切哪種模式，建議可以選擇tmux。可以參考網路上各種教學文件，例如：  
https://blog.longwin.com.tw/2011/04/tmux-learn-screen-config-2011/  
比較古老的虛擬終端機程式是screen，他也有等效byobu的status bar的設定模式。編輯~/.screenrc即可完成。這邊就也不另外說明，請讀者自行google。

## Windows SSH連Ubuntu server 工具：Putty

如果你的Ubuntu是裝在虛擬機上面，有ssh連線讓你連入工作是很重要的。通常最多人用的SSH連線工具是Putty，網路上教學也很多，這邊也不贅述。Putty搭配byobu的好處是在server上進行的工作到一半可以暫停，僅client斷線關機，並不會干擾到server上進行改code的進度。而且你平常工作仍然離不開Windows環境的話，那有SSH連線工具就更重要了。

同時server端也應該要安裝ssh server，如果沒有安裝ssh server請安裝openssh-server  
sudo apt install openssh-server

## Diff軟體

除了git 內建diff功能，Linux的工具程式diff也很常使用。尤其是有兩包相似的code需要做對比檢驗變更狀況的時候。diff -ruN大家也很常用。如果你用Linux桌面環境，meld也蠻好用的。Windows上就有beyondcompare這種付費軟體可用。

