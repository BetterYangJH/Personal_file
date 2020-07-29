```js
function sirtWord(row){
    var english =[]
    var chinese =[]
    var re =new RegExp("^[\u4e00-\u9fa5]")
    //分离中英文
    for(I=0;I<row.length;I++){
        if(!re.test(row[i]){
            english.push()
        } else {
            Chinese.push()
        }   
   //英文转大写排序
   english.sort(
        (a,b)=>a.toUpperCase().charCodeAt(0)-b.toUpperCase().charCodeAt(0)
   )
   //中文按拼音首字母排序
   chinese.sort(
        (a,b)=>a.localCompare(b)
    english.push.apply(Chinese)
    return english  
}    
```

