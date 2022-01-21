---
title: php实现敏感词过滤（Trie树）
author: HiuTingYin
date: 2019-12-27 20:55:00 +0800
categories:  [日常问题合集, PHP]
tags: PHP
excerpt: 项目需求需要对敏感词进行过滤，需要精确匹配、模糊匹配，并且要根据不同时期（例如重大节假日），或是不同级别的项目，对敏感词的校验严格度不同，进一步进行处理。
---

####    代码适用场景：

1.特殊时间需要大规模针对某些敏感词进行敏感词检测

2.敏感词除了精确匹配外还需要模糊匹配，如傻lkaj瓜

3.针对不同时期（例如重大节假日），或者是不同级别的项目，对敏感词的校验严格度不同，进行进一步处理。

#### 实现逻辑
通过前缀树/字典树 算法，通过利用字符串的公共前缀来节约存储空间。

例如当前敏感词数组为：['傻瓜','傻瓜蛋','傻子']

当要匹配的字符串中含有 '傻瓜'、'傻子'时，下图字典树示例中的红色边框则为对应的终止节点。

字典树如图所示：

![image.png](https://image-static.segmentfault.com/259/120/2591209789-5e0eb5873e7f2_articlex)

首先需要通过敏感词字典文件将敏感词初始化字典树，
然后在字典树上搜索添加过的字符串。

其步骤如下：  

1.从根结点开始搜索。

2.取得要查找字符串的第一个字符，根据该字符选择对应的字符路径向下继续搜索。

3.如果字符串搜索完成后，判断当前是否已经是对应敏感字符路径的终止节点，如果是的话，说明字典树中含有该字符串，反正说明不含有该字符串。

4.如果想要添加模糊匹配的话，可以在对应字符路径判断的逻辑中，增加允许跳过的字符串长度判断。

**敏感词等级处理**
通过敏感词校验等级，来更灵活的控制屏蔽词的力度。
- 一级屏蔽词
  校验字符串中只要顺序包含屏蔽词，则都屏蔽。
  如敏感词：“傻瓜”，“你是不是傻啦吧唧瓜哪” -->“你是不是\*啦吧唧\*哪”
  
```
public function index()  
{  
     $logic = new filterWords();  
     $str = $logic->filter('你是不是傻啦吧唧瓜哪',1);  
     echo '校验结果：' . $str;  
}
```
```
校验结果：你是不是*啦吧唧*哪
```
- 二级屏蔽词
  校验字符串中只要顺序间隔n个字符内包含屏蔽词，则屏蔽。
  如敏感词：“傻瓜”，间隔2个字符内屏蔽。
  “你是不是傻啦吧瓜哪” -->“你是不是\*啦吧\*哪”
  “你是不是傻啦吧唧瓜哪” -->“你是不是傻啦吧唧瓜哪”
```
public function index()  
{  
     $logic = new filterWords();  
     $str = $logic->filter('你是不是傻啦吧瓜哪',2,2);  
     echo '校验结果：' . $str;  
}
```
```
校验结果：你是不是*啦吧*哪
```
- 三级屏蔽词
  校验字符串中只要全词匹配屏蔽词，则屏蔽。
  如敏感词：“傻瓜”。
  “你是不是傻瓜哪” -->“你是不是\*\*哪”
  “你是不是傻啦吧唧瓜哪” -->“你是不是傻啦吧唧瓜哪”
```
public function index()  
{  
     $logic = new filterWords();  
     $str = $logic->filter('你是不是傻瓜哪',3);  
     echo '校验结果：' . $str;  
}
```
```
校验结果：你是不是**哪
```  

#### 思路流程图：
![image.png](https://image-static.segmentfault.com/489/516/489516922-5e05aa3838fcf_articlex)
  
  
#### 封装成一个工具类：filterWords.php  
```  
<?php  
  
class filterWords  
{  
 protected $dict;//敏感词字典  
  
 public function __construct() 
 { 
    $this->loadDataFormFile();
 }  
 
 /** 
  * 从文件中加载敏感词字典  
  */
  protected function loadDataFormFile() 
  { 
      //此处可以修改为读文件，一般敏感词为文件形式，一行对应一个敏感词  
     //如果经常调用的话，还可以通过缓存处理（redis、memcache）等等，此处不详细处理  
     $arr = [ 
         '笨蛋',  
         '傻瓜',  
     ]; 
     //将敏感词加入此次节点  
     foreach ($arr as $value) { 
        $this->addWords(trim($value)); 
     } 
 }  
 /** 
 * 分割文本  
 * @param $str 
 * @return array[]|false|string[] 
 */ 
 protected function splitStr($str)
 { 
     //将字符串分割成组成它的字符  
     // 其中/u 表示按unicode(utf-8)匹配（主要针对多字节比如汉字），否则默认按照ascii码容易出现乱码  
     return preg_split("//u", $str, -1, PREG_SPLIT_NO_EMPTY); 
 }  
 
 /** 
 * 添加敏感字至节点  
 * @param $words 
 */
 protected function addWords($words) 
 { 
     //1.分割字典  
     $wordArr = $this->splitStr($words);
     $curNode = &$this->dict; 
     foreach ($wordArr as $char) { 
         if (!isset($curNode)) { 
            $curNode[$char] = []; 
         } 
        $curNode = &$curNode[$char]; 
     } 
     //标记到达当前节点完整路径为"敏感词"  
     $curNode['end']++; 
 }  
 
 /**  
  * 敏感词校验  
  * @param $str ;需要校验的字符串  
  * @param int $level ;屏蔽词校验等级 1-只要顺序包含都屏蔽；2-中间间隔skipDistance个字符就屏蔽；3-全词匹配即屏蔽  
  * @param int $skipDistance ;允许敏感词跳过的最大距离，如笨aa蛋a傻瓜等等  
  * @param bool $isReplace ;是否需要替换，不需要的话，返回是否有敏感词，否则返回被替换的字符串  
  * @param string $replace ;替换字符  
  * @return bool|string  
  */
 public function filter($str, $level = 1, $skipDistance = 2, $isReplace = true, $replace = '*')  
{  
     //允许跳过的最大距离  
     if ($level == 1) {  
         $maxDistance = strlen($str) + 1;  
     } elseif ($level == 2) {  
         $maxDistance = max($skipDistance, 0) + 1;  
     } else {  
         $maxDistance = 2;  
     }
     $strArr = $this->splitStr($str); 
     $strLength = count($strArr);
     $isSensitive = false;
     for ($i = 0; $i < $strLength; $i++) {
         //判断当前敏感字是否有存在对应节点  
         $curChar = $strArr[$i]; 
         if (!isset($this->dict[$curChar])) { 
             continue; 
         }
         $isSensitive = true; //引用匹配到的敏感词节点  
         $curNode = &$this->dict[$curChar]; 
         $dist = 0; 
         $matchIndex = [$i]; //匹配后续字符串是否match剩余敏感词  
         for ($j = $i + 1; $j < $strLength && $dist < $maxDistance; $j++) {
             if (!isset($curNode[$strArr[$j]])) { 
                $dist++; continue; 
             } 
            //如果匹配到的话，则把对应的字符所在位置存储起来，便于后续敏感词替换  
             $matchIndex[] = $j; 
             //继续引用  
             $curNode = &$curNode[$strArr[$j]];
        }  
        //判断是否已经到敏感词字典结尾，是的话，进行敏感词替换  
         if (isset($curNode['end']) && $isReplace) { 
             foreach ($matchIndex as $index) { 
                $strArr[$index] = $replace;
             }
             $i = max($matchIndex);
         } 
        } 
         if ($isReplace) { 
            return implode('', $strArr);
         } else { 
            return $isSensitive;
         }
     }
 }  
```

```
灵感来自于 [嘉兴ing](https://segmentfault.com/a/1190000019137933 "Trie树 php 实现敏感词过滤") 
感谢分享.

本文主要是针对上文添加了自己的理解，以及增加了通过屏蔽等级灵活控制敏感词过滤。
```