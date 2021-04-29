---
title: SM2国密算法-学习札记
date: 2020-12-05 10:56:59
tags: 工作记录
copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1607147252248&di=104ea22af9037f168010e3828afa5d98&imgtype=0&src=http%3A%2F%2Fsem.g3img.com%2Fg3img%2Fszhuawei1%2Fc2_20180514111250_73457.jpg
---
国密算法是我国自主研发创新的一套数据加密处理系列算法。从SM1-SM4分别实现了对称、非对称、摘要等算法功能。特别适合应用于嵌入式物联网等相关领域，完成身份认证和数据加解密等功能。当然，默认的前提条件是算法密钥必须保证安全性，因此要将国密算法嵌入到硬件加密芯片中结合使用。
# 算法介绍
国密即国家密码局认定的国产密码算法。主要有SM1，SM2，SM3，SM4。密钥长度和分组长度均为128位。
- SM1 为对称加密。其加密强度与AES相当。该算法不公开，调用该算法时，需要通过加密芯片的接口进行调用。
- SM2为非对称加密，基于ECC。该算法已公开。由于该算法基于ECC，故其签名速度与秘钥生成速度都快于RSA。ECC 256位（SM2采用的就是ECC 256位的一种）安全强度比RSA 2048位高，但运算速度快于RSA。
- SM3 消息摘要。可以用MD5作为对比理解。该算法已公开。校验结果为256位。
- SM4 无线局域网标准的分组数据算法。对称加密，密钥长度和分组长度均为128位。
由于SM1、SM4加解密的分组大小为128bit，故对消息进行加解密时，若消息长度过长，需要进行分组，要消息长度不足，则要进行填充。