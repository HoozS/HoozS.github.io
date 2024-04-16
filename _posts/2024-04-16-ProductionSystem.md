---
layout:     post
title:      "产生式系统实验"
subtitle:   " 基于Python的产生式动物识别系统"
date:       2024-04-16 18:00:00
author:     "Hooz"
header-img: "img/post-bg-2015.jpg"
tags:
    - Python
    - 产生式系统
---

# 产生式系统

把一组产生式放在一起，让它们互相配合，协同作用，一个产生式生成的结论可以供另一个产生式作为已知事实使用，以求得问题的解，这样的系统称为产生式系统。
![20240416182019](https://raw.githubusercontent.com/HoozS/pic/main/pic/20240416182019.png)

## 文件读取

建立规则库文件rules.txt

```text
有毛发 哺乳动物
有奶 哺乳动物
有羽毛 鸟
会飞 会生蛋 鸟
生活在水中 水生动物
吃肉 肉食动物
有犬齿 有爪 眼盯前方 肉食动物
哺乳动物 有蹄 有蹄类动物
哺乳动物 反刍 有蹄类动物
哺乳动物 肉食动物 黄褐色 暗斑点 金钱豹
哺乳动物 肉食动物 黄褐色 黑色条纹 虎
哺乳动物 水生动物 有齿 海豚
哺乳动物 水生动物 无齿 须鲸
有蹄类动物 有角 角不分叉 山羊
有蹄类动物 有角 角分叉 鹿
有蹄类动物 长脖子 长腿 暗斑点 长颈鹿
有蹄类动物 黑色条纹 斑马
水生动物 有鳞 有鳃裂 鲨鱼
水生动物 无鳞 鲇鱼
鸟 长脖子 长腿 不会飞 黑白二色 鸵鸟
鸟 会游泳 不会飞 黑白二色 企鹅
鸟 会游泳 会飞 天鹅
鸟 肉食动物 鹰
鸟 会飞 信天翁
```

用以下代码读取

```python

def loadRules(filename):  
    rules = {}  
    with open(filename, 'r', encoding='utf-8') as f:  
        rule_index = 1  # 从1开始计数规则   

        for line in f:  
            rule_content = line.strip()  # 直接将整行作为规则内容   

            rules[rule_index] = rule_content.split(' ') # 用空格分割   
            
            rule_index += 1  
    return rules

```

## 推理

### 建立特征库

用内存中的变量定义为特征库，是其初始为空。

```python

# 特征库列表  
features = []    
features.extend(feature.split())   

```

### 推理执行

外层循环中进行顺序，在内层循环获取规则，若符合则输出当前规则，将当前规则的索引加入已找到的索引列表foundRules，将当前规则的最后一项，结论部分加入特征库并输出，将符合规则标志ruleFound置真并跳出循环。

```python

while True:  
    ruleFound = False  
    for rule_index, rule_content in rules.items():  
        # 如果这条规则已经被找到过，则跳过  
        if rule_index in foundRules:  
            continue  
        # 检查规则中的条件是否都在特征库中  
        if all(item in features for item in rule_content[:-1]):  
            # 输出使用的规则及其条件  
            print("rule{} : if {} then {}".format(rule_index, ' and '.join  (rule_content[:-1]), rule_content[-1]))  
            # 将找到的规则序号加入到已找到的规则列表中  
            foundRules.append(rule_index)  
            # 将新的特征加入到特征库中   
            features.append(rule_content[-1])  
            print("特征库：", features)  
            ruleFound = True  
            break  
    # 如果没有找到新的规则，返回失败   
    if not ruleFound:  
        print("无法推断新的结论。")   
        return ("推理失败。")   
    # 检查是否已经找到最终规则   
    if max(foundRules) >= 10:  
        return features[-1]   

```

### 跳出判断

由于初始设置为一定规则之后为最终规则，找到就跳出，如果没有找到任何新的规则，就跳出并输出错误信息。

```python

# 如果没有找到新的规则，返回失败   
if not ruleFound:  
    print("无法推断新的结论。")  
    return ("推理失败。")  
# 检查是否已经找到最终规则  
if max(foundRules) >= 10:  
    return features[-1]  

```

## 结果

![20240416183232](https://raw.githubusercontent.com/HoozS/pic/main/pic/20240416183232.png)
