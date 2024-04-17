### **什麼是 NTTU-NOTIFY** What is NTTU-NOTIFY
NTTU-NOTIFY 是一個 LINE Notify 服務，結合了 NTTU 的公告。  
我花了9個小時使用 `Google Apps Script` 來製作它。  
它可以接收 Line 使用者的 `token` 來建立與 NTTU-NOTIFY 的連接。  
我無法保證它永遠不會丟失任何公告。  
如果您在使用 NTTU-NOTIFY 時有任何不愉快的經歷，您可以隨時解除綁定。  

This is a LINE Notify Service which combined the NTTU Announcement.  
I spent  9 hours to make it by Google Apps Script.   
It could take `the Line User's token` to establish a connection with NTTU-NOTIFY.  
I can't promise you that it never loss any announcement.  
`If you experience any unpleasantness while using NTTU-NOTIFY, you can unbind at any time.`

<br/>
<br/>
<br/>

### 演算法
發現有紀錄數字重複出現，最後發現是因為行政人員會修改公告。導致找不到過去相符去更新。  
因此，設計先去更新目前公告於紀錄的數字，並且在過程中紀錄剩餘的位置與未更新的新公告。  
如果是正常順序的更新(剩餘總量-剩餘的最大值=1。從0開始計算，剩餘最大值+1應為剩餘總量)，  
儲存位置為[5-x]，第一則公告覆蓋第五則舊公告，第二則公告覆蓋第四則舊公告...。  
若為非，則於剩餘值=>位置，放入未更新的公告。保證不會產生重複值。  

<img width="800" height="400" src="https://github.com/SmallliDinosaur/NTTU-NOTIFY/blob/main/picture/%E5%85%AC%E5%91%8A%E7%88%AC%E5%8F%96.png?raw=true"/>

```
var existingValues = sheet1.getRange(inputRange).getValues().flat().map(String); // 取得對應範圍的值
var outputValues = sheet1.getRange(outputRange).getValues().flat().map(Number); // 获取 outputRange 的所有值
var count=0;
for (var i = 0; i < result.length && i < 5; i++) {   
  var cellValue = result[i];                          //爬蟲的內容
  if (existingValues.includes(cellValue)) {          //已經存在公告更新第幾個
    var index = existingValues.indexOf(cellValue);
    sheet1.getRange(outputRange).getCell(index + 1, 1).setValue(i + 1);
    var ii= visit.indexOf(i);
    var iii= nolist.indexOf(index);
    visit.splice(ii, 1);
    nolist.splice(iii, 1);
  } 
}
for (var i = 0; i < visit.length; i++) {   
  x=visit[i];
  var cellValue = result[x];
  count+=1;
  if(Math.max(visit)-1==visit.length){
    for (var j = 0; j < outputValues.length; j++){
      if (outputValues[j] == (5-x)) {
        sheet1.getRange(outputRange).getCell(j + 1, 1).setValue(x + 1);
        sheet1.getRange(inputRange).getCell(j + 1, 1).setValue(cellValue);
        //製作訊息內容
        if(count==1){
          message += (whoami+'\n'+cellValue);
        }
        else{
          message += ('\n\n'+cellValue);
        }
      }
    }
  }
  else{
    sheet1.getRange(outputRange).getCell(nolist[i] + 1, 1).setValue(x + 1);
    sheet1.getRange(inputRange).getCell(nolist[i] + 1, 1).setValue(cellValue);
    //製作訊息內容
    if(count==1){
      message += (whoami+'\n'+cellValue);
    }
    else{
      message += ('\n\n'+cellValue);
    }
  }
}
```

<br/>
<br/>
<br/>

### **如何建立連接** How to establish a connection
1. 首先，前往**連結**。 （建議您先複製鏈接，然後使用**Chrome 瀏覽器無痕**登入。）  
First, go to the **link**. (It is recommended that you copy the link first and then use Chrome to log in incognito.)  
     * https://lihi2.com/oHjjP
2. 點選藍色連結。  Click the Blue Link.
<img width="400" height="450" src="https://hackmd.io/_uploads/H1efPgiJA.png"/>
3. 然後您將看到 LINE 登入圖片。 Then you will see the LINE login picture.
<img width="400" height="450" src="https://hackmd.io/_uploads/ByUFteikR.png"/>
4. 使用 LINE Notify 進行一對一聊天。 1-on-1 chat with LINE Notify.
<img width="400" height="450" src="https://hackmd.io/_uploads/Bk9oHeoJR.png"/>
5. 最後成功顯示連線畫面。 Finally, successfully displayed connection screen.
<img width="400" height="450" src="https://hackmd.io/_uploads/HJgsFxskC.png"/>


<br/>
<br/>

若想要解除綁定：
https://notify-bot.line.me/my/ 

<br/>
<br/>

### **服務更新列表**
- 2024/04/04 4hr
    - 更改顯示 `NTTU-NOTIFY` -> `NTTU` ，精簡顯示。產品名稱仍為`NTTU-NOTIFY`。
- 2024/04/08 6hr
    - 起床發現`學術公告`漏抓，原來是勤奮的`研發處`，更該抓取頻率。
    - 驚覺重複推送公告，因此設計`演算法`解決這問題。
- 2024/04/10 3hr
    - RRR~ 今天怎麼還有重複的。哭阿 ~ 原來是全域變數`區域變數`阿。
- 2024/04/12 4hr
    - 謝謝，我發現演算法順序大亂，因為發出去的公告會修正，導致 Old 位置不被更新。
- 2024/04/13 4hr
    - 重複順序問題，我從新設計。謝謝ˊˋ。
<br/>
<br/>
<br/>


## Reference
* [~~Aws-Lambda~~](https://www.ecloudture.com/deploy-line-chatbot-using-aws-lambda-1/)
* [LINE Notify API Document](https://notify-bot.line.me/doc/en/)
* [Google Apps Script, The element type "meta" must be terminated by the matching end-tag "</meta>"](https://stackoverflow.com/questions/70283834/google-apps-script-the-element-type-meta-must-be-terminated-by-the-matching-e)
    * Solution: Use `Regular Expressions`。
* [Images from Google drive in embedded HTML no longer working](https://support.google.com/sites/thread/253003338/images-from-google-drive-in-embedded-html-no-longer-working?hl=en)
    * Google Apps Script cannot display `<img>` in HTML. There are other parameters but they look blurry. Finally, `<iframe>` is easy to use and can be zoomed.


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

:exclamation::question: 
- **別想叫窩搞系辦公告，佬資不想做。** 
- **校長口不口以贊助窩!!!** :heart:
- 今天是 04/04 台灣祭，我依然[堅守本校](https://youtu.be/kyhthTloAIg?si=D5V--lwKxOxVaJUd)，沒有去墾丁瘋狂EEOO。  
    臺東時間 : 2024/04/04 為校站崗 [為清純打卡](https://youtu.be/Z7i7iZnZ0vQ?si=jpssSeNuAQXtb6JX)。可是我，可是[我](https://youtu.be/VBlGbFOGffY?si=rqXfjtx6XEqhguP_&t=135)...🤡

 
<br/>
<br/>

<img width="700" height="500" src="https://hackmd.io/_uploads/S18iBbleA.png"/>
<img width="700" height="500" src="https://hackmd.io/_uploads/HknkIZxlC.png"/>


<br/>
<br/>
