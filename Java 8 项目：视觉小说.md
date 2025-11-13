# Java 8 项目：视觉小说
## 引言
本篇主要讲的是如何写出视觉小说运算逻辑，并且能处理不同好感度，从而触发不同的剧情。
>后端新人，有错误和可改善的地方欢迎各位大佬提出，我会改进项目的
## 1.运算逻辑
### 1.1 静态
我们知道，静态变量是跟随类的，静态变量的值一旦改变是永久的，除特殊情况要重置其值，所以，我们用**静态变量**来作为**亲密度**的载体，这样方便程序运行途中进行加减。

例如：
```Java
static int intimacy = 0;
```
即，**静态变量会保存对其的更改**
### 1.2  工具
在整串代码中，像是 “ 请选择 ” 和 “ 面对如此情势，你决定： ”这种需要多重复的代码，可以构建一个快捷工具的父类，其他类继承这个父类，从而可以用 **super** 调用父类方法，从而减轻负担。

例如：
```Java
public static class tool {  
    public void select(){  
        System.out.println("请选择");  
    }  
    public void decided(){  
        System.out.println("面对如此情势，你决定：");  
    }  
}
```
### 1.3 交互
视觉小说最重要的肯定是能和用户建立起联系，所以我们需要用到 **Scanner** 关键字，它可以帮助我们读取用户输入的内容，从而进行处理
```Java
import java.util.Scanner;

...

Scanner read = new Scanner(System.in)

...
```


### 1.4 主干
此项目中，除了第一块代码是需要用到 **main** 方法的，后面都可以新建一个继承 **tool** 的子类，从这个子类里面创建方法，当然，处理亲密度的方法除外，这样子步骤虽然繁琐，但是思路简单，项目不容易出错。

我们来看 **main** 方法的内部
```Java
public static void main(String[] args) {  
    System.out.println("背景故事");  
    try(Scanner read = new Scanner(System.in)) {  
       while (true){  
           System.out.println("面对xxx，你决定：");  
           System.out.println("1.xxx 2.xxx");  
           System.out.println("请选择：");  
  
           if (!read.hasNext()){  
               System.out.println("请正确输入");  
               read.nextLine();  
               continue;  
           }  
  
           int one = read.nextInt();  
           read.nextLine();  
  
           switch (one){  
               case 1:  
                   System.out.println("选择一的结果");  
                   break;  
               case 2:  
                   System.out.println("选择二的结果");  
                   Son er = new Son();  
                   er.first(read);  
                   break;  
               default:  
                   System.out.println("没有这个选择，当然也可以不加");  
                   break;  
           }  
       }  
    }catch (Exception e){  
        System.out.println("发生未知问题" + e.getMessage());  
    }  
}
```
从这串代码可以清楚的看到我们 **main** 方法内部的处理机制，整体使用了 **try-catch** 和 **switch** ，用法基础，应该方便理解，因为在我的代码里，一选项是会直接死掉的，所以我采用了 **while** ，最后面会有整个项目的代码示例。选择反馈里的 **son** 就是我们后面创建的 **tool** 的子类。

来看后面的代码：
```Java
public static class Son extends tool {  
    public void first(Scanner read){  
        System.out.println("事件");  
        super.decided();
        System.out.println("1.xxx " +  
                "2.xxx " +  
                "3.xxx");  
        super.select();  
  
        if (!read.hasNext()){  
            System.out.println("请正确输入");  
            read.nextLine();  
        }  
  
        int two = read.nextInt();  
        read.nextLine();  
  
        switch (two){  
            case 1:  
                intimocy += 10;  
                System.out.println("选择一的结果");  
                second(read);  
                return;  
            case 2:  
                intimocy += 5;  
                System.out.println("选择二的结果");  
                second(read);  
                return;  
            case 3:  
                intimocy -= 10;  
                System.out.println("选择三的结果");  
                second(read);  
                return;  
        }  
    }
}
```
显而易见，后面都可以在 **Son 类**中进行套娃，直到故事的结束，**switch** 选项结果那边，可以对亲密度进行加减，从而影响后续故事。
### 1.5 亲密度处理
这算是视觉小说最核心的部分了，也是相当麻烦的，为了体现最近所学，我使用了**枚举**这个方法，这个方法很好的处理的亲密度区间，从而对不同区间进行不同返回结果。 
我们来看代码：
```Java 
public enum processing{  
    //处理枚举的方法  
  QINMIDU_HIGH(80,200),  
    QINMIDU_LOW(0, 79);  
 
    private final int min;  
    private final int max;  
  
    processing(int min,int max){  
        this.min = min;  
        this.max = max;  
    }  
  
    public static processing comparison(int numerical){  
        for (meijv digital : values()){  
            if(numerical >= digital.min && numerical <= digital.max){  
                return digtal;  
            }  
        }  
        return QINMIDU_LOW;  
    }  
}
```
这串代码处理亲密度，将亲密度放在对应的枚举范围内，但肯定，只有这一串代码是必然不够的，还得有指定范围返回指定值的代码，才能让我们的目的实现。

如下：
```Java
public static void Strategyone(int numerical){  
    processing word = processing.comparison(numerical);  
  
    switch (word){  
        case QINMIDU_LOW:  
            System.out.println("亲密度低的结果");  
            break;  
        case QINMIDU_HIGH:  
            System.out.println("亲密度高的结果");  
            break;  
    }  
}
```
不过，如果是处理不同对象的话，那肯定是不能触发的结果都一样的，所以我们需要多创建方法，来达到处理不同对象的目的，当然，也不要忘记在main方法中添加这个引用这个方法处理对应的对象的亲密度。

## 结尾实例
最后的话，就是项目实际代码，略显生涩，还请各位多多包容
>这个项目代码的许多命名和上面的举例的命名都不尽相同，均是使用拼音，海涵。（故事是ai跑的，实在是幻想不到）
```Java
package org.example;  
import java.util.Scanner;  
  
public class gxzlhd {  
    //好感度，用静态保存更改  
  static int xuejie = 0;  
    static int tongzhuo = 20;  
    static  int xuemei = 40;  
  
    public enum meijv{  
        //处理枚举的方法  
  QINMIDU_GAO(80,200),  
        QINMIDU_DI(0, 79);  
  
        private final int min;  
        private final int max;  
  
        meijv(int min,int max){  
            this.min = min;  
            this.max = max;  
        }  
  
        public static meijv bijiao(int qinmidu){  
            for (meijv huoqv : values()){  
                if(qinmidu >= huoqv.min && qinmidu <= huoqv.max){  
                    return huoqv;  
                }  
            }  
            return QINMIDU_DI;  
        }  
    }  
  
    //便携工具  
  public static class Gongjv {  
        public void xaunze(){  
            System.out.println("\n请选择");  
        }  
        public void hui(){  
            System.out.println("\n面对如此情势，你决定：");  
        }  
    }  
  
    //开篇  
  public static void main(String[] args) {  
        System.out.println("头脑眩晕......\n" +  
                "上一世我熬夜打代码猝死了\n" +  
                "这一世好像重生在熬夜学习猝死的高中生\n" +  
                "重来一次我绝对不负韶华\n" +  
                "回想原主的记忆，发现原主有个高三的学姐女神\n" +  
                "有个关系好的女同桌，还有追求原主的高一学妹\n" +  
                "原主对这三个都有点爱慕，但是良心告诉他只能追求一个\n" +  
                "于是你决定既然在这副身体上重生，那就给原主一个交代\n" +  
                "帮原主从这三个里面选一个");  
        try(Scanner huifu = new Scanner(System.in)) {  
           while (true){  
               System.out.println("\n想完这些事情后，你看着凌乱的书桌于是：");  
               System.out.println("\n1.继续学习 2.赶紧睡觉");  
               System.out.println("\n请选择：");  
  
               if (!huifu.hasNext()){  
                   System.out.println("请正确输入");  
                   huifu.nextLine();  
                   continue;  
               }  
  
               int one = huifu.nextInt();  
               huifu.nextLine();  
  
               switch (one){  
                   case 1:  
                       System.out.println("成功再次猝死，恭喜获得成就：\"我死两次\"");  
                       break;  
                   case 2:  
                       System.out.println("第二天.....");  
                       Diertian er = new Diertian();  
                       er.shijianyi(huifu);  
                       break;  
                   default:  
                       System.out.println("不跟随选项，你有取死之道");  
                       break;  
               }  
               duigy(xuemei);  
               duitz(tongzhuo);  
               duigs(xuejie);  
           }  
        }catch (Exception e){  
            System.out.println("发生未知问题" + e.getMessage());  
        }  
    }  
  
    //不同角色不同处理亲密度方法  
  public static void duigs(int qinmidu){  
        meijv shu = meijv.bijiao(qinmidu);  
  
        switch (shu){  
            case QINMIDU_DI:  
                System.out.println("高三学姐对你提不起兴趣，全力备战高考\n" +  
                        "学姐如愿的考上了自己心仪的大学\n" +  
                        "-----学姐篇遗憾收场-----");  
                break;  
            case QINMIDU_GAO:  
                System.out.println("面对你和她经历的种种，她依然道心稳固\n" +  
                        "学姐努力备考，最后成功考上自己心仪的大学\n" +  
                        "或许学姐会在某一天想起曾经和谁有过某种经历\n" +  
                        "你也在想是否真的如那句话一样\n" +  
                        "\"白月光本就是爱而不得的\"\n" +  
                        "与此同时你也暗下决心\n" +  
                        "-----学姐篇遗憾收场-----");  
                break;  
        }  
    }  
  
    public static void duitz(int qinmidu){  
        meijv shu = meijv.bijiao(qinmidu);  
  
        switch (shu){  
            case QINMIDU_GAO:  
                System.out.println("一起同学这么长时间，还经历了那么多\n" +  
                        "很难不资生情愫，于是你们顺理成章\n" +  
                        "但天底下哪有不透风的墙\n" +  
                        "你们之间的事情被老师发现\n" +  
                        "请家长、休学，但这些都没有将你们分开\n" +  
                        "虽然高考失利，不过庆幸的是你们考上了同一所大学\n" +  
                        "往后的日子虽然平凡，但过的十分充实与开心\n" +  
                        "-----同桌篇永恒真挚-----");  
                break;  
            case QINMIDU_DI:  
                System.out.println("纵然同桌许久，但看对不上眼也依旧白搭\n" +  
                        "以后可能会分道扬镳，但这种校园生活却不一定会忘记\n" +  
                        "-----同桌篇不失为美-----");  
                break;  
        }  
    }  
  
    public static void duigy(int qinmidu){  
        meijv shu = meijv.bijiao(qinmidu);  
  
        switch (shu){  
            case QINMIDU_DI:  
                System.out.println("你为忙于自己的事情，对向你示爱的学妹不予理会\n" +  
                        "你毕业后，想在大学闯出一片天地，于是便加入了学生会\n" +  
                        "大二时，你负责接待新生，而新生中一道熟悉的背影让你回忆起高中那个在楼梯口徘徊等你下课的人\n" +  
                        "-----学妹篇意料之外-----");  
                break;  
            case QINMIDU_GAO:  
                System.out.println("你在学妹满满的爱意中久久不能回复自我\n" +  
                        "就此，你沦陷了，高考落榜，整日萎靡不振\n" +  
                        "但学妹好像并不在乎，你消沉一段时间后觉得自己不能再这样\n" +  
                        "于是忙于事业，虽然陪学妹的时间减少了，但并不会因此而冷落\n" +  
                        "不过你渐渐就发现很多奇怪的点，为什么公司一直蒸蒸日上\n" +  
                        "觉得创业并不是网上说的那样难，你和学妹说，学妹也只是在一旁笑着，并没有说什么\n" +  
                        "-----学妹篇佳偶天成-----");  
        }  
    }  
  
    //第二天  
  public static class Diertian extends Gongjv {  
        public void shijianyi(Scanner huifu){  
            System.out.println("早上久违的妈妈叫你起床的声音\n" +  
                    "你在恍惚间穿完衣服、洗漱、吃饭....\n" +  
                    "等再次回过神面前是严肃的脸\"拿着书来前面站着读\"\n" +  
                    "原来已经迟到了，无奈之下只好照做\n" +  
                    "回到座位，忽然传来一阵女声\n" +  
                    "\"呦，头一次见你来这么晚啊\"\n" +  
                    "回忆里原主虽然学习到很晚，但是来的都是很准时的");  
            System.out.println("\n面对同桌的调侃，你决定：");  
            System.out.println("\n1.要你管，好好读你的书 " +  
                    "2.昨晚学习太晚了，不小心睡过头 " +  
                    "3.只是笑笑，没有回应");  
            super.xaunze();  
  
            if (!huifu.hasNext()){  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int two = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (two){  
                case 1:  
                    tongzhuo += 10;  
                    System.out.println("老师看讲台下面吵吵嚷嚷的，不像读书的，于是去查看情况\n" +  
                            "发现你和同桌正在激情对喷，于是将你们赶出教室，并罚站一天\n" +  
                            "恭喜获得成就：\"煤气罐\"\n");  
                    shijianer(huifu);  
                    return;  
                case 2:  
                    tongzhuo += 5;  
                    System.out.println("同桌愣了一下，随后露出理解的笑容\n" +  
                            "\n原来是这样啊，我还以为你转性了呢\n" +  
                            "她悄悄递给你一颗糖果，\"提提神\"\n");  
                    shijianer(huifu);  
                    return;  
                case 3:  
                    tongzhuo -= 10;  
                    System.out.println("同桌见你只是笑笑，觉得你好无趣\n" +  
                            "\"切，没意思\"，她转过头去不再理你\n");  
                    shijianer(huifu);  
                    return;  
            }  
        }  
  
        public void shijianer(Scanner huifu){  
            System.out.println("下课铃声响起，你收拾书包准备回家\n" +  
                    "楼梯转角处，一个娇小的身影突然出现\n" +  
                    "'学长！这个...这个给你！'\n" +  
                    "学妹塞给你一盒手工饼干，脸红着跑开了\n" +  
                    "原主记忆中，这个学妹从高一入学就对你格外关注\n");  
            super.hui();  
            System.out.println("\n1.追上学妹，和她聊天 " +  
                    "2.收下饼干，默默记住这份心意 " +  
                    "3.追上她把饼干还回去");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int three = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (three){  
                case 1:  
                    xuemei += 20;  
                    xuejie -= 3;  
                    tongzhuo -= 3;  
                    System.out.println("你追上学妹，她惊讶地停下来\n" +  
                            "你们聊了很久，发现她其实很了解你的喜好\n" +  
                            "分别时，学妹脸红着说：\"学长，明天还能见到你吗？\"\n");  
                    shijiansan(huifu);  
                    return;  
                case 2:  
                    xuemei += 10;  
                    System.out.println("你看着手中的饼干，心里有些复杂\n" +  
                            "原主的记忆告诉你，学妹家境优越但性格内向\n" +  
                            "这份心意让你既感动又压力山大\n");  
                    shijiansan(huifu);  
                    return;  
                case 3:  
                    xuemei -= 15;  
                    System.out.println("学妹的眼神瞬间暗淡，低着头离开了\n" +  
                            "你看到她转身时擦了擦眼睛，心里有些过意不去\n");  
                    shijiansan(huifu);  
                    return;  
            }  
        }  
  
        public void shijiansan(Scanner huifu){  
            System.out.println("周末去图书馆自习，在书架间看到了熟悉的身影\n" +  
                    "高三的学姐正专注地翻阅参考书，阳光洒在她认真的侧脸上\n" +  
                    "她似乎遇到了难题，微微蹙眉\n");  
            super.hui();  
            System.out.println("\n1.主动上前提供帮助 " +  
                    "2.默默放一瓶水后离开 " +  
                    "3.装作没看见继续找书");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int four = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (four){  
                case 1:  
                    xuemei -= 3;  
                    xuejie += 10;  
                    System.out.println("学姐惊讶地看了你一眼，随后接受了你的帮助\n" +  
                            "解题过程中，你发现她其实很聪明，只是太紧张了\n" +  
                            "\"谢谢，你...很厉害\"她轻声说道\n");  
                    shijiansi(huifu);  
                    return;  
                case 2:  
                    xuejie += 5;  
                    System.out.println("学姐发现水时，你已离开\n" +  
                            "第二天，你在课桌里发现了一张感谢纸条\n" +  
                            "字迹清秀工整，一如她给人的感觉\n");  
                    shijiansi(huifu);  
                    return;  
                case 3:  
                    System.out.println("你继续找自己的书，但心里却想着学姐刚才认真的样子\n" +  
                            "原主的记忆中，学姐一直是高不可攀的存在\n");  
                    shijiansi(huifu);  
                    return;  
            }  
        }  
  
        public void shijiansi(Scanner huifu){  
            System.out.println("同桌戳了戳你的胳膊，小声说：\n" +  
                    "\"这道数学题我不会，能教教我吗？\"\n" +  
                    "她不好意思地低下头，耳根微微发红\n");  
            super.hui();  
            System.out.println("\n1.耐心讲解直到她明白 " +  
                    "2.我现在没空，找别人吧 " +  
                    "3.简单说下思路就结束");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int five = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (five){  
                case 1:  
                    tongzhuo += 15;  
                    xuejie -= 5;  
                    System.out.println("同桌认真听讲，终于弄懂了题目\n" +  
                            "\"你讲得比老师还好！\"她崇拜地看着你\n" +  
                            "从此她经常找你问题，你们的关系越来越近\n");  
                    shijianwu(huifu);  
                    return;  
                case 2:  
                    tongzhuo -= 10;  
                    System.out.println("同桌失落地转过头去，一整节课都没再理你\n" +  
                            "下课铃响，她默默收拾书包，没有像往常一样和你说再见\n");  
                    shijianwu(huifu);  
                    return;  
                case 3:  
                    tongzhuo += 5;  
                    System.out.println("同桌似懂非懂，但还是向你道了谢\n" +  
                            "\"那个...明天能再给我讲一遍吗？\"她小声请求\n");  
                    shijianwu(huifu);  
                    return;  
            }  
        }  
  
        public void shijianwu(Scanner huifu){  
            System.out.println("学妹在校门口等你，手里拿着精致的请柬\n" +  
                    "\"学长，这周六是我的生日派对，你能来吗？\"\n" +  
                    "她的眼神中充满期待\n");  
            super.hui();  
            System.out.println("\n1.爽快答应并准备礼物 " +  
                    "2.以学习为由婉拒 " +  
                    "3.答应但只短暂出席");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int six = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (six){  
                case 1:  
                    xuemei += 20;  
                    xuejie -= 5;  
                    tongzhuo -= 8;  
                    System.out.println("学妹高兴地跳起来，然后害羞地跑开了\n" +  
                            "生日派对上，你发现她家确实很富裕\n" +  
                            "但她对你的态度始终真诚而热情\n");  
                    shijianliu(huifu);  
                    return;  
                case 2:  
                    xuemei -= 15;  
                    System.out.println("学妹失落地低下头，小声说\"那好吧\"\n" +  
                            "接下来的几天，你都没在楼梯口看到她的身影\n");  
                    shijianliu(huifu);  
                    return;  
                case 3:  
                    xuemei += 10;  
                    tongzhuo -= 3;  
                    System.out.println("学妹虽然有点失望，但还是开心地说了时间和地点\n" +  
                            "派对上你只待了半小时，但她依然很感激你的到来\n");  
                    shijianliu(huifu);  
                    return;  
            }  
        }  
  
        public void shijianliu(Scanner huifu){  
            System.out.println("偶然听到学姐和朋友的对话：\n" +  
                    "\"马上就要高考了，压力好大...\"\n" +  
                    "她看起来有些疲惫，眼下有淡淡的黑眼圈\n");  
            super.hui();  
            System.out.println("\n1.发送鼓励的短信 " +  
                    "2.准备提神的小礼物放在她桌上 " +  
                    "3.觉得不该打扰她备考");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int seven = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (seven){  
                case 1:  
                    xuejie += 10;  
                    System.out.println("学姐回复了\"谢谢\"，虽然简短\n" +  
                            "但你知道这是她第一次主动回复你的消息\n" +  
                            "这小小的进步让你雀跃不已\n");  
                    shijianqi(huifu);  
                    return;  
                case 2:  
                    xuejie += 15;  
                    tongzhuo -= 5;  
                    System.out.println("学姐发现礼物后四处张望，你躲在远处没有现身\n" +  
                            "第二天，你发现她看你的眼神柔和了许多\n");  
                    shijianqi(huifu);  
                    return;  
                case 3:  
                    System.out.println("你默默走开，没有打扰她\n" +  
                            "但心里总觉得自己错过了什么重要的机会\n");  
                    shijianqi(huifu);  
                    return;  
            }  
        }  
        public void shijianqi(Scanner huifu){  
            System.out.println("运动会期间，同桌在比赛中扭伤了脚\n" +  
                    "学妹拉着你要去给她班级加油\n" +  
                    "学姐则在看台上安静地复习\n");  
            super.hui();  
            System.out.println("\n1.送同桌去医务室 " +  
                    "2.陪学妹去加油 " +  
                    "3.去找学姐讨论学习问题");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int eight = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (eight){  
                case 1:  
                    tongzhuo += 20;  
                    xuejie -= 5;  
                    xuemei -= 10;  
                    System.out.println("同桌靠在你的肩上，你扶着她慢慢走向医务室\n" +  
                            "\"有你在真好\"她轻声说道，你们的关系因此拉近很多\n");  
                    shijianba(huifu);  
                    return;  
                case 2:  
                    xuemei += 15;  
                    tongzhuo -= 10;  
                    System.out.println("学妹开心地拉着你的手跑到她们班级的位置\n" +  
                            "但她注意到你心不在焉的样子，笑容渐渐暗淡\n");  
                    shijianba(huifu);  
                    return;  
                case 3:  
                    xuejie += 15;  
                    xuemei -= 5;  
                    tongzhuo -= 5;  
                    System.out.println("学姐对你来找她感到有些意外\n" +  
                            "\n你不是应该去参加运动会吗？\n她问道，眼神中带着疑惑\n");  
                    shijianba(huifu);  
                    return;  
            }  
        }  
  
        public void shijianba(Scanner huifu){  
            System.out.println("期末考试临近，三个人都对你有所期待：\n" +  
                    "同桌想和你一起复习，学妹约你去图书馆，学姐需要你帮忙整理资料\n");  
            super.hui();  
            System.out.println("\n1.和同桌组成学习小组 " +  
                    "2.陪学妹去图书馆 " +  
                    "3.帮学姐整理资料");  
            super.xaunze();  
  
            if (!huifu.hasNext()) {  
                System.out.println("请正确输入");  
                huifu.nextLine();  
            }  
  
            int nine = huifu.nextInt();  
            huifu.nextLine();  
  
            switch (nine){  
                case 1:  
                    tongzhuo += 20;  
                    xuejie -= 15;  
                    xuemei -= 15;  
                    System.out.println("你们每天一起复习，同桌的成绩提高很快\n" +  
                            "\"要是早点和你一起学习就好了\"她感慨道\n");  
                    return;  
                case 2:  
                    xuemei += 25;  
                    tongzhuo -= 15;  
                    xuejie -= 15;  
                    System.out.println("学妹在图书馆总是偷偷看你\n" +  
                            "你发现她其实很聪明，只是在你面前会装作不懂\n");  
                    return;  
                case 3:  
                    xuejie += 25;  
                    xuemei -= 15;  
                    tongzhuo -= 15;  
                    System.out.println("学姐在你的帮助下很快整理好了资料\n" +  
                            "\"你比我想象中要可靠\"她难得地夸赞了你\n");  
                    return;  
            }  
        }  
    }  
}
```