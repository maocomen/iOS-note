### NSString 

***

* #### NSStringCompareOptions

  * `NSCaseInsensitiveSearch`

    不区分大小写

  * `NSLiteralSearch`

    区分大小写

  * `NSBackwardsSearch`

    从字符串末尾开始搜索

  * `NSAnchoredSearch`

    搜索限制范围内的字符串

  * `NSNumericSearch`

    按照字符串里的数字为依据，算出顺序。例如 Foo2.txt < Foo7.txt < Foo25.txt

  * `NSDiacriticInsensitiveSearch`

    忽略 "-" 符号的比较

  * `NSWidthInsensitiveSearch`

    忽略字符串的长度，比较出结果（不太理解）

  * `NSForcedOrderingSearch`

    忽略不区分大小写比较的选项，并强制返回 NSOrderedAscending 或者 NSOrderedDescending

  * `NSRegularExpressionSearch`

    只能应用于 rangeOfString:..., stringByReplacingOccurrencesOfString:...和 replaceOccurrencesOfString:... 方法。使用通用兼容的比较方法，如果设置此项，可以去掉 NSCaseInsensitiveSearch 和 NSAnchoredSearch

* #### NSStringTransform

  * `NSStringTransformLatinToKatakana`

     拉丁文转片假名（日语）

  * `NSStringTransformLatinToHiragana`

    拉丁文转平假名（日语

  * `NSStringTransformLatinToHangul`

    拉丁文转韩语

  * `NSStringTransformLatinToArabic`

    拉丁文转阿拉伯语

  * `NSStringTransformLatinToHebrew`

    拉丁文转希伯来语

  * `NSStringTransformLatinToThai`

    拉丁文转泰语

  * `NSStringTransformLatinToCyrillic`

    拉丁文转西里尔语

  * `NSStringTransformLatinToGreek`

    拉丁文转希腊语

  * `NSStringTransformToLatin`

    转成拉丁文

  * `NSStringTransformMandarinToLatin`

    普通话转拉丁文

  * `NSStringTransformHiraganaToKatakana`

    平假名（日语）转片假名（日语）

  * `NSStringTransformFullwidthToHalfwidth`

    全角转半角

  * `NSStringTransformToXMLHex`

    转成XML十六进制字符

  * `NSStringTransformToUnicodeName`

    为Unicode字符命名

  * `NSStringTransformStripCombiningMarks`

    去掉结合符号（重音和变音符）

  * `NSStringTransformStripDiacritics`

    去掉变音符

* #### Extension

  - `(NSString *)stringByTrimmingCharactersInSet:(NSCharacterSet *)set;`

    给字符串去除特殊符号

  - `(NSString *)stringByPaddingToLength:(NSUInteger)newLength withString:(NSString *)padString startingAtIndex:(NSUInteger)padIndex;`

    创建一个新字符串,长度为length,旧字符串从索引字符开始拷贝到旧字符串后面

  - `(NSString *)stringByFoldingWithOptions:(NSStringCompareOptions)options locale:(nullable NSLocale *)locale NS_AVAILABLE(10_5, 2_0);`

    返回经过 options 筛选之后的字符串

  - `@property (readonly, copy) NSString *uppercaseString;`

    返回大写字符串

  - `@property (readonly, copy) NSString *lowercaseString;`

    返回小写字符串

  - `@property (readonly, copy) NSString *capitalizedString;`

    返回字符串 串中的每个单词的首字母大写,其余字母小写

  - `@property (readonly, copy) NSString *localizedUppercaseString NS_AVAILABLE(10_11, 9_0);`

    本地化大写字符串

  - `@property (readonly, copy) NSString *localizedLowercaseString NS_AVAILABLE(10_11, 9_0);`

    本地化小写字符串

  - `@property (readonly, copy) NSString *localizedCapitalizedString NS_AVAILABLE(10_11, 9_0);`

    本地化字符串，串中每个单词首字母大写，其余字母小写



### NSCharacterSet

---

* `controlCharacterSet`

  控制符

* `whitespaceCharacterSet`

  空格

* `whitespaceAndNewlineCharacterSet`

  空格换行

* `decimalDigitCharacterSet`

  小数

* `letterCharacterSet`

  文字

* `lowercaseLetterCharacterSet`

  小写字母

* `uppercaseLetterCharacterSet`

  大写字母

* `nonBaseCharacterSet`

  非基础

* `alphanumericCharacterSet`

  字母数字

* `decomposableCharacterSet`

  可分解

* `illegalCharacterSet`

  非法

* `punctuationCharacterSet`

  标点

* `capitalizedLetterCharacterSet`

  大写

* `symbolCharacterSet`

  符号

* `newlineCharacterSet`

  换行


### NSException

---

抛异常的几种方式

* ```objective-c
  [NSException raise:@"ExceptionName" format:@"ExceptionReason"];
  ```

* ```objective-c
  @throw ([NSException exceptionWithName:@"ExceptionName" reason:@"ExceptionReason" userInfo:nil]);
  ```

* ```objective-c
  NSException *EX = [NSException exceptionWithName:@"ExceptionName" reason:@"ExceptionReason" userInfo:nil];
  [EX raise];
  ```

* ```objective-c
  - (void)tryCatch
  {
      @try {
          //有可能崩溃的代码
      }
      @catch (NSException *exception) {
          //捕获异常
      }
      @finally {
          //无论有没有崩溃都会执行
      }
      //如果catch里抛出了异常，就不会执行
  }
  ```


#### 打电话的几种方式

个人所知两种：

* 一，用webView打电话，会先弹出提示框，是否呼叫。。。key为tel:1888888
* 二，[[UIApplication sharedApplication] openURL]打电话
  * key为tel:`188****8888`，iOS10.3之前不会弹出提示框，10.3之后和其他方式没什么区别
  * key为`telprompt://188****8888`，会弹提示框

#### 格林威治时间转换

NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];

​        [dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ss.000Z"];

​        NSDate *localDate = [dateFormatter dateFromString:time];

​        //设置源日期时区

​        NSTimeZone* sourceTimeZone = [NSTimeZone timeZoneWithAbbreviation:@"GMT"];//或GMT

​        //设置转换后的目标日期时区

​        NSTimeZone* destinationTimeZone = [NSTimeZone localTimeZone];

​        //得到源日期与世界标准时间的偏移量

​        NSInteger sourceGMTOffset = [sourceTimeZone secondsFromGMTForDate:localDate];

​        //目标日期与本地时区的偏移量

​        NSInteger destinationGMTOffset = [destinationTimeZone secondsFromGMTForDate:localDate];

​        //得到时间偏移量的差值

​        NSTimeInterval interval = destinationGMTOffset - sourceGMTOffset;

​        //转为现在时间

​        NSDate* destinationDateNow = [[NSDate alloc] initWithTimeInterval:interval sinceDate:localDate];

​        resultTime = [TKDateHelper formatDate:destinationDateNow format:@"yyyy-MM-dd HH:mm"];