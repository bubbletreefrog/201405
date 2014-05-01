## Z > b 還是 Z < b (作者：Wush Wu)

最近"Z"和"b"的比較鬧得沸沸揚揚，身為R的重度使用者，當然也要來好好的探討一下這個問題。立馬來試試看：


```r
"Z" > "b"
```

```
## [1] TRUE
```


沒錯，R語言在這兩者的比較上是支持 *Z大於b* 的！

作者我呢也對此抱持著堅定不移的信念，直到某天我在linux上使用crontab跑的R程式出現了Bug。

這個Bug非常的隱晦，最困難的地方在於，當我開Rstudio or R console來跑的時候，結果是正確的，但是當我把它丟到crontab下跑的時候，結果卻完全不同。

經過層層追尋之後，我發現問題就在 *Z大於b* 上啊。在crontab裡， *Z小於b* ！

這是怎麼回事？原來這和locale有關。根據`?Comparison`的描述：

```
The collating sequence of locales such as en_US is normally different from C (which should use ASCII) and can be surprising.
```

也就是說，當locale（語系）這個環境變數不同的時候，R在做字母的比較是不同的。當語系設定為如"en\_US"或"zh\_TW"的時候，大小順序應是: `A > B > ... > Z > a > b > ... > z`，但是當語系設為"C"的時候，R  會把字元轉成對應[的ASCII](http://en.wikipedia.org/wiki/ASCII)的整數做比較而此時Z是`0x5a`，b是`0x62`，所以Z > b就錯了。

眼就為憑，我們馬上做點實驗：





```r
Sys.setlocale(locale = "zh_TW")
```

```
## [1] "zh_TW/zh_TW/zh_TW/C/zh_TW/zh_TW.UTF-8"
```

```r
"Z" > "b"
```

```
## [1] TRUE
```

```r
Sys.setlocale(locale = "C")
```

```
## [1] "C/C/C/C/C/zh_TW.UTF-8"
```

```r
"Z" > "b"
```

```
## [1] FALSE
```





而一般我們裝R之後，預設會使用如en\_US或zh\_TW等語系，但是crontab這類環境卻是使用C這個語系。

也因此，我們可以得出結論：

> Z大於b不一定是對的，一切都要看你身處的環境啊！

### 作者 : Wush Wu ([wush978@gmail.com](mailto:wush978@gmail.com))

- [Taiwan R User Group](https://www.facebook.com/Tw.R.User) Organizer
- R 相關著作：
    - [RMessenger](http://cran.r-project.org/web/packages/RMessenger/index.html)的作者
    - [RSUS](https://bitbucket.org/wush_iis/rsus)，這是[On Shortest Unique Substring Query](http://www.cs.sfu.ca/~jpei/publications/MISQ_ICDE12.pdf)的實作
- 研究領域：Large Scale Learning，[Text Mining](http://www.cs.sfu.ca/~jpei/publications/MISQ_ICDE12.pdf)和[Uncertain Time Series](http://www.cs.sfu.ca/~jpei/publications/Shortest%20Unique%20Substring%20Queries%20ICDE13.pdf)

