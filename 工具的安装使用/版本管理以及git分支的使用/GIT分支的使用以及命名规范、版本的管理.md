# GIT分支的使用以及命名规范、版本的管理

## GIT分支的使用

|    分支     |        命名        |                             说明                             |
| :---------: | :----------------: | :----------------------------------------------------------: |
|   主分支    |       master       |  主分支，所有提供给用户使用的正式版本，都在这个主分支上发布  |
|  开发分支   |        dev         |              开发分支，永远是功能最新最全的分支              |
|  功能分支   |     feature-*      | 新功能分支，某个功能点正在开发阶段 ，*命名为 feature_**${Author}**_**${yyMMdd}**_**${功能名称}** |
|  发布版本   |     release-*      |                     发布定期要上线的功能                     |
|  修复分支   |      hotfix-*      |                      修复线上代码的 bug                      |
| release修复 | **release-fix** _* |                      修复release上的bug                      |

1. 主分支： master分支有且仅有一个，用于存储正式发布的历史，在版本库初始化时自动生成。master分支是所有开发活动的核心分支，所有的开发活动产生的输出物最终都会反映到主分支的代码中。 

2. dev分支有且仅有一个，develop分支用来分布重大版本，是所有功能的集成分支。当dev分支上的代码已实现了软件需求说明书中所有的功能，通过了所有的集成测试后，且代码已经足够稳定时，就可以将dev合并到master分支进行发布。

   注：master和develop分支的权限需要限制。

3. feature分支会有很多个，功能分支是为了开发某种特定功能，从master分支上面分出来的。开发完成后，并经过test分支测试后，最终合并到develop。

   feature分支命名规范：feature**_${Author}****_${yyMMdd}**_**${功能名称}** 

   注意：feature分支要定期合并master的代码（一般一周一次）

4. 发布上线前的测试版本，命名规范为：release-v2.0.1， 版本号（要有对应的版本号表）

5. hotfix分支会有多个，系统日常维护难免会存在bug，这时就需要创建一个分支，进行bug修补。bugfix分支是从master分支上面分出来的，在对bug修补之后，要先合并到develop分支进行测试，测试通过后再合并进master分支，最终发布master分支。

   bug-fix分支命名规范：bugfix_${**Author**}_${yyMMdd}${补丁名称} 

6. release-fix分支，用于修复测试版本上的bug，命名规范为release-fix_${**Author**}_${yyMMdd}${补丁名称}

## 版本管理

### version定义规则

主版本号：表示项目的重大架构变更
次版本号：表示较大范围的功能添加和变化
增量版本号：一般表示重大bug修复
eg： 0.0.1-snapshot、2.1.12-release、2.4.14-alpha.3

### 单词含义

+ snapshot 快照
+ alpha 内测
+ beta 公测
+ release 稳定版本 
+ GA 最稳定版本 
+ Final 正式版 
+ Pro(professional) 专业版 
+ Plus 加强版 
+ Retail 零售版 
+ DEMO 演示版 
+ Build 内部标号
+ Delux 豪华版 （deluxe：豪华的，华丽的）
+ Corporation或Enterpraise 企业版 
+ M1 M2 M3 M是milestone的简写 里程碑的意思 
+ RC 版本 RC:(Release Candidate)，几乎就不会加入新的功能了，而主要着重于除错 
+ SR 修正版 
+ Trial 试用版 
+ Shareware 共享版 
+ Full 完全版 

