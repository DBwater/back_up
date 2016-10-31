---
title: 正则表达式引擎的构建（1）
date: 2016-10-19 19:44:02
tags: [编译原理]
---
学习自己写一个简单的正则表达式引擎

<!--more-->

## 正则表达式
正则符号| 含义		 
-------|-----------------
\*     | 匹配的是1个或多次 
?      | 匹配0次或1次      
\+     | 匹配0次或者多次  
｜     | 并行符


此文章建立在你已经掌握基本的正则表达式和自动机的相关知识，并具有有扎实的数据结构前提下。关于自动自动机的原理可以参考[轮子哥的博客](http://www.cppblog.com/vczh/archive/2008/05/22/50763.html)和其他相应的关于自动机的书籍。

### 首先我们来看一组简单的正则表达a(bb)+a的匹配过程:
***
1.首先我们可以根据正则表达式构造一个简答的自动机
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig0.png)
2.然后我们可以看一个关于字符串abbbba的匹配的过程,状态读取第一个字符，a,它在开始状态s0。跟着一个箭头到状态是s1。状态机读取余下的字符串继续上面的过程：b到s2，b到s3，b到s2，b到s3，最后a到s4。
![](http://img.blog.csdn.net/20141202222427706?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2luZ292ZXJ0aGVjbG91ZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  状态机在S4结束，所以匹配这个字符串。如果在一个非匹配状态结束，就没有达成匹配。如果在状态机执行的任意时刻，如果没有箭头来转移当前的字符，状态机提早结束。
本章我们只考虑关于NFA(非确定性有限自动机)，下一篇将会讲述如何把NFA转换成DFA，提高自动机匹配的效率。
都知道一般简答的正则表达式都包括上面列表的匹配符号*,?,+以及并行符|。
那么我们可以得到相应的正则表达式的NFAs
## 正则表达式转换成NFA
匹配单个字符：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig4.png)

e1e2的连接NFA，是把e1的最后箭头和e2的头部相连：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig5.png)

e1|e2的选择NFA，添加一个新的开始状态来二选一:
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig6.png)

e?的NFA在e和一条空的路径间选择：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig7.png)

e\*的NFA用了同样的选择，但是循环匹配e回到最初：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig8.png)

e+的NFA同样构造循环，但是至少通过e一次：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig9.png)
本正则表达式引擎基于Thompson算法，具体的算法过程可参考上面轮子哥的blog
第一步我们可以先把所给的正则表达式（中缀表达式）转换为后缀表达式(这一部分就不做描述，可以直接观看完整源代码)，你可以用一个特殊符号来表示连接预算符号，这里离我们用.来表示，如“a(bb)+a”转换一个等效的后缀表达式“abb.+.a”。
# 第一步:构建NFA
下面我们用一种简单的结构体来表示NFA的一个节点
```c
	struct State { 
		int c; 				
 		State*out;                                                                                       
  		State*out1; 
  		intlastlist;
	};
```
 

每个状态代表三个NFA局部之一，由c值决定。Lastlist在下面会解释
![](http://7xkyoa.com1.z0.glb.clouddn.com/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F1.png)
当编译器查看后缀表达式时，维持了NFA局部的一个栈。依次把NFA局部压入栈中，如果遇到运算符，把NFA局部弹出栈，然后压入一个新的局部。例如，编译完abb的abb.+.a，栈中包含了a,b,b的NFA局部。编译到“.”时，把两个b的局部弹出，然后压入一个bb的连接。每个NFA的局部定义为其实状态和它的转移箭头：
```c
struct Frag
{
  State *start; //表示局部的开始状态节点
  Ptrlist *out;//表示局部的尾节点因为尾节点可能分叉，所以需要用一个list来表示。
}
```
遍历已经生成的后缀表达式，通过一个辅助栈能够把所有局部合并为一个局部，最后补上一个结束的匹配状态就完成了基本DFA的构建。	
```c
State* post2nfa(char *postfix)
{
	char *p;
	Frag stack[1000], *stackp, e1, e2, e;
	State *s;
	// fprintf(stderr, "postfix: %s\n", postfix);
	if(postfix == NULL)
		return NULL;
	#define push(s) *stackp++ = s
	#define pop() *--stackp
	stackp = stack;
	for(p=postfix; *p; p++){
		switch(*p){
		//这部分单独拿出来分析。		
		}
		
	}
	e = pop();
	if(stackp != stack)
		return NULL;
	patch(e.out, &matchstate);
	return e.start;
#undef pop
#undef push
}
```
为文字字符的时候：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig14.png)
```c
default: 
  s = state(*p, NULL, NULL);                                                                                       
  push(frag(s, list1(&s->out)); 
  break; 
```
为连接符号.的时候：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig15.png)
```c
case '.': 
  e2 = pop();                                                                                       
  e1 = pop(); 
  patch(e1.out, e2.start); 
  push(frag(e1.start, e2.out)); 
  break;
```
为选择符号|的时候：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig16.png)
```c
case '|': 
  e2 = pop();                                                                                       
  e1 = pop(); 
  s = state(Split, e1.start, e2.start); 
  push(frag(s, append(e1.out, e2.out))); 
  break;
```
为重复0次或者1次?时候:
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig17.png)
```c
case '?': 
  e = pop();                                                                                       
  s = state(Split, e.start, NULL); 
  push(frag(s, append(e.out, list1(&s->out1)))); 
  break; 
```
当重复0次或多次\*：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig18.png)
```c
case '*': 
  e = pop();                                                                                       
  s = state(Split, e.start, NULL); 
  patch(e.out, s); 
  push(frag(s, list1(&s->out1))); 
  break; 
```
重复1次或多次+：
![](http://pdos.csail.mit.edu/~rsc/regexp-img/fig19.png)
```c
case '+': 
  e = pop();                                                                                       
  s = state(Split, e.start, NULL); 
  patch(e.out, s); 
  push(frag(e.start, list1(&s->out1))); 
  break; 
```
到此NFA已经构造完成了，现在我们就要让字符串在这个NFA上跑了。
# 第二步：模拟字符串在NFA上跑
我们需要跟踪记录状态机状态的集合，把他存在一个简单的线性表中：
```c
struct List                                                                                       
{ 
  State **s;
  int n; 
}; 
```
   addstate添加一个状态到列表中，但是已经在里面了不会添加。每一次添加的时候都要遍历整个列表很低效；实际上listid作为列表的生成数。当addstate添加s，它在s->lastlist记录listid。如果两者相同，s在列表创建时已经在其中了。
```c
void addstate(List *l, State *s)                                                                                       
{ 
  if(s == NULL || s->lastlist == listid) 
         return;  
  s->lastlist = listid; 
  if(s->c == Split){ 
    //当s为连接点不为转移的状态时 
         addstate(l, s->out);  
         addstate(l, s->out1);  
         return;  
  } 
  l->s[l->n++] = s; 
} 
```
startlist通过加入开始状态创建初始列表：

```c
List*  startlist(State *s, List *l)                                                                                        
{ 
  listid++; 
  l->n = 0; 
  addstate(l, s);//把s状态加入到l表中
  return l; 
} 
```

然后我们可以用两个表一个clist记录状态机当前状态的集合,一个表nlist记录状态机下一步将会到达的状态集合，刚开始clist显然是只有一个开始状态的。然后模拟状态机的转移。
  为了避免在每一次迭代中申请内存， match用两个预先申请的表l1和l2作为clist和nlist，每一步交换二者。

```c
int match(State *start, char *s) 
{ 
  List *clist, *nlist, *t;                                                                                        			
  clist = startlist(start, &l1); //l1,l2是预先分配的表	
  nlist = &l2; 
  for(; *s; s++){ 
         step(clist, *s, nlist); //走到下一个状态 
         t = clist; clist = nlist; nlist = t;  //因为只要保存当前状态所以可以通过交换clist,nlist来保存clist
  } 
  return ismatch(clist); 
}
```
step用单个字符推进NFA，用当前列表clist计算后续列表nlist。
```c
void step(List *clist, int c, List *nlist) 
{ 
  int i; 
  State *s;                                                                                       
   
  listid++; 
  nlist->n = 0; 
  for(i=0; i<clist->n; i++){ 
         s = clist->s[i];  
         if(s->c == c)  
                 addstate(nlist, s->out);  
  } 
} 
```
  如果最后的状态列表包含匹配状态，就达成了匹配。
```c
int ismatch(List *l)                                                                                       
{ 
  int i; 
   
  for(i=0; i<l->n; i++) 
         if(l->s[i] == matchstate)  
                 return 1;  
  return 0; 
}
```
到此一个NFA匹配的正则表达式引擎就成功了。有兴趣继续研究如果把NFA转换成DFA的同学可以参考下一篇文章。
[完整源代码](https://github.com/DBwater/regex_compile/blob/master/NFA_regex.cpp)










