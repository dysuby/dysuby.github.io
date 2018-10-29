---
layout: post
title:  AVL树简介及C++实现
date:   2017-10-26
categories: ds
tags: [tree,ds,avl]
---

## AVL树简介
AVL树是一棵**自平衡的二叉查找树**，由*G.M. Adelson-Velsky*和*E.M. Landis*在他们1962 年的论文*《An algorithm for the organization of information》*中发表。

<!--more--> 

### 什么是自平衡
普通的二叉查找树虽然遍历简单，但是存在一个问题：树的结构容易变坏。比如，当插入序列为`[1, 2, 3, 4]`时，普通的二叉查找树便会退化为普通链表；又比如，当对一棵完全二叉查找树进行多次删除时，不管是使用前驱删除（即用被删节点的前驱节点替代该位置）或后继删除（用后继节点替代），都会使树偏向某一边。此时，为了解决这个问题，AVL树使用了*旋转*方法实现自平衡。

![不平衡的二叉树](/images/avltree/unbalance.png)

这是一棵典型的“坏树”

![AVL树](/images/avltree/avlExample.png)

这是一棵“好树”

---

## 平衡条件
### 高度（height）
**高度**为每个节点都具有的属性，其值为该节点**左右孩子的高度的最大值 + 1**，而空节点的高度在这里定义为-1。由此推出，叶子节点高度为0。
### 何时需要调整
当一个节点左右儿子的高度差大于等于（实际上等于即可）2时，则判定该节点不平衡，并对其进行*旋转*。

![不平衡树](/images/avltree/unbalance2.png)

上图中'23'节点左儿子的高度为-1，而右儿子的高度为1，相差为2，故不平衡。

---

## 旋转
**旋转**，个人认为形容很贴切的一个词，当插入（删除）结束后，跳出递归时便要对各节点（也就是被操作节点到根的路径上的节点）进行判断是否平衡，当不平衡时，便以该节点为目标进行**旋转**，**旋转**会`return`一个`node*`，以将新的子树传递回去。

考虑这样一颗初始树：

![初始AVL树](/images/avltree/initial.png)

根据插入节点高度的不同，可以分为四种情况：
### LeftLeft
当插入一个值为'1'的节点时树将变得不再平衡，'8'的左儿子高度为2，右儿子的高度为0。

![](/images/avltree/leftleft1.png)

因为节点是其父亲的左儿子，其父亲又是上一节点的左儿子，故而称为*Leftleft*的情况以方便记忆，此时的**旋转**为**左单旋转**（只旋转一次）

![](/images/avltree/leftleft2.png)

可以形象的理解为图中三个节点顺时针转了一圈，'8'转了之后变成了'4'的右儿子，所以原先的右儿子便要“换爸爸”，变成'8'的左儿子，这就完成了旋转。

![](/images/avltree/leftleft3.png)

### RightRight
如果理解了*leftleft*，那*rightright*也不难理解，只是情况变成了节点是父亲的右儿子，父亲又是上一节点的右儿子。现在对树插入'35'

![](/images/avltree/rightright1.png)

可见'29'的左右儿子高度差为2，需要**右单旋转**。

![](/images/avltree/rightright2.jpg)

此时，'29'变为'32'的新左儿子（因为没有原左儿子，故较为易懂，但实现上还是需要一视同仁）

![](/images/avltree/rightright3.png)

以上便是*旋转*的基本操作，而接下来的两种情况只是以上两种单旋转的复合罢了。
### LeftRight
*leftright*意为节点为其父亲的右儿子，但是其父亲为其上一节点的左儿子，此时不管是左单旋转还是右单旋转，都不能使树平衡（可以自己尝试验证），所以要对树进行两次旋转。首先对上面的树插入'25'，插入后平衡故不需旋转。但是此时插入'27'，则树会变成下图的不平衡状态

![](/images/avltree/leftright1.png)

既然这不是熟知的情况，那就先将其变成熟知的情况。下面的节点'25'进行一次右旋转

![](/images/avltree/leftright2.png)
![](/images/avltree/leftright3.png)

现在就可以对'29'进行熟悉的左旋转了

![](/images/avltree/leftright4.png)
![](/images/avltree/leftright5.png)

这样便可以得到一棵新的平衡树了。

### RightLeft
对新的平衡树插入'61'，此时树处于平衡，然后插入'55'

![](/images/avltree/rightleft1.png)

现在即使*rightleft*情况，被插入节点是其父亲的左儿子，其父亲是上一节点的右儿子。类似*leftright*，可以先进行一次左旋转，使其变为熟知的*rightright*

![](/images/avltree/rightleft2.png)
![](/images/avltree/rightleft3.png)

现在再进行一次右旋转

![](/images/avltree/rightleft4.png)
![](/images/avltree/rightleft5.png)

这样便可以实现*rightleft*的平衡了。

### 对谁进行*旋转*
就插入而言，下面几种情况都是对被插入节点而言，但如果是删除的话，就不存在这种节点了，那么删除又是对谁而言呢？就个人的理解，*旋转*的真正情况分类应对**不平衡的节点**而言，即*leftleft*是指不平衡节点的左儿子高度比右儿子大，而左儿子的左儿子也比左儿子的右儿子高度大，简单来说，**引起不平衡的节点在左儿子的左子树上**（可能有点绕，可以结合图理解）。类似地，*rightright*是因为不平衡节点在右儿子的右子树上，以此类推*leftright*和*rightleft*。这样子理解更容易看懂下面的删除如何选择旋转。

---

## 类的设计
节点是用名为`node`的`struct`保存，里面除了其元素和左右孩子指针，还有一个额外的高度。
AVL树是名为`avl`的`class`，提供插入删除，以及前中后序的遍历，`private`成员有四种旋转方法，并有一个`height(node*)`返回节点高度，避免了多余的节点是否为空的讨论。
因为使用的递归插入和删除需要传递节点，故真正的插入删除放在了`private`中，而用户只需使用`insert(int)`和`remove(int)`即可。
### 插入
在这里使用的是递归插入，在插入后判断是否平衡，并更新高度。
### 删除
在这里使用了后继删除，并用引用传入节点，减少了将父节点更新的麻烦，也避免了内存泄漏。删除分为三种情况：
- 被删除节点有右儿子也有右儿子，则将其右子树上的最小节点替代被删除节点的位置。也就是说，如果右儿子有左儿子，则一直顺着左儿子遍历下去，直到找到“最左”的儿子；如果右儿子没有左儿子，则直接用右儿子代替被删除节点的位置。

![](/images/avltree/first.png)

例如图中要删除根节点'41'的话，将'49'代替掉'41'。

![](/images/avltree/first2.png)

- 被删除节点只有一个儿子，则直接让该儿子代替被删除节点的位置。例如，同样是上面的树，如果要删除'20'的话，用'29'代替

![](/images/avltree/second.png)

- 被删除节点为叶子节点，直接删除。

第一种情况换个思路可以看成删除**需要替代被删除节点的节点**，然后把这个节点的值赋给被删除节点，这样便变相的“删除”了目标。这样想的好处是第一种情况又可以调用自身去删除替代节点而不用去考虑高度的更新和重新平衡子树。而最终都可以达到第三种或者第二情况从而停止递归。（至于是否需要多余的时间代价我没有去仔细想）

---

## Test
前期：利用gdb与[https://visualgo.net/en](https://visualgo.net/en)进行逐步对比，用时最长但也是能找出bug的笨方法
后期：虽然说后期其实也就用时1个早上而已，自己写了个随机数插入并删除，并在删除后用队列BFS检查节点，然后手动运行n次，至于内存检查使用valgrind
![结果](/images/avltree/memorycheck.png)

---

## src
```c++
#include <iostream>

#include <random>
#include <ctime>
#include <queue>
#include <vector>
#include <algorithm>
#define TEST_SIZE 1000

using namespace std;

struct node {
  int ele;
  node *left;
  node *right;
  int height;
  node(int i, node *l, node *r, int h): 
      ele(i), left(l), right(r), height(h) {}
};

class avl {
  private:
    node *root;

    node* leftleft(node *T) {
      node *temp = T -> left;
      T -> left = temp -> right;
      temp -> right = T;
      T -> height = ((height(T -> left) > height(T -> right)) ? height(T -> left) : height(T -> right)) + 1;
      temp -> height = ((T -> height > height(temp -> left)) ? T -> height : height(temp -> left)) + 1;
      return temp;
    }
    node* rightright(node *T) {
      node *temp = T -> right;
      T -> right = temp -> left;
      temp -> left = T;
      T -> height = ((height(T -> left) > height(T -> right)) ? height(T -> left) : height(T -> right)) + 1;
      temp -> height = ((T -> height > height(temp -> right)) ? T -> height : height(temp -> right)) + 1;
      return temp;
    }
    node* leftright(node *T) {
      T -> left = rightright(T -> left);
      return leftleft(T);
    }
    node* rightleft(node *T) {
      T -> right = leftleft(T -> right);
      return rightright(T);
    }
    int height(node* t) {
      if (t == 0)
        return -1;
      else
        return t -> height;
    }

    node* realInsert(int i, node *T) {
      if (T == 0) {
        T = new node(i, 0, 0, 0);
      } else if (i < T -> ele) {
        T -> left = realInsert(i, T -> left);
        if (height(T -> left) - height(T -> right) == 2)
          if (i < T -> left -> ele)
            T = leftleft(T);
          else
            T = leftright(T);
      } else if (i > T -> ele) {
        T -> right = realInsert(i, T -> right);
        if (height(T -> right) - height(T -> left) == 2)
          if (i > T -> right-> ele)
            T = rightright(T);
          else
            T = rightleft(T);
      }
      T -> height = ((height(T -> left) > height(T -> right)) ? height(T -> left) : height(T -> right)) + 1;
      return T;
    }

    bool realRemove(node *&T, int i) {
      if (!T)
        return false;
      if (i < T -> ele) {
        if (realRemove(T -> left, i) == false) {
          return false;
        }
        else if (height(T -> right) - height(T -> left) == 2) {
          if (height(T -> right -> left) <= height(T -> right -> right))
            T = rightright(T);
          else
            T = rightleft(T);
        } else {
          T -> height = ((height(T -> left) > height(T -> right)) ? height(T -> left) : height(T -> right)) + 1;
        }
      } else if (i > T -> ele) {
        if (realRemove(T -> right, i) == false) {
          return false;
        }
        else if (height(T -> left) - height(T -> right) == 2) {
          if (height(T -> left -> right) <= height(T -> left -> left))
            T = leftleft(T);
          else
            T = leftright(T);
        } else {
            T -> height = ((height(T -> left) > height(T -> right)) ? height(T -> left) : height(T -> right)) + 1;
        }
      } else {
        if (T -> right && T -> left) {
          node *temp = T -> right;
          if (temp -> left) {
            while (temp -> left && temp -> left -> left) {
              temp = temp -> left;
            }
            int key = temp -> left -> ele;
            node *oldT = T;
            realRemove(T, key);
            oldT -> ele = key;
          } else {
            int key = temp -> ele;
            node *oldT = T;
            realRemove(T, key);
            oldT -> ele = key;
          }
        } else if (T -> left || T -> right) {
          node *temp = (T -> left) ? T -> left : T -> right;
          delete T;
          T = temp;
        } else {
          delete T;
          T = 0;
        }
      }
      return true;
    }
  public:
    avl(): root(0) {}
    ~avl() {
      clear();
      root = 0;
    }
    
    void preorder(void(*fun)(node*), node* T) {
      if (T == 0)
        return;
      fun(T);
      preorder(fun, T -> left);
      preorder(fun, T -> right);
    }
    void postorder(void(*fun)(node*), node* T) {
      if (T == 0)
        return;
      postorder(fun, T -> left);
      postorder(fun, T -> right);
      fun(T);
    }
    void inorder(void(*fun)(node*), node* T) {
      if (T == 0)
        return;
      inorder(fun, T -> left);
      fun(T);
      inorder(fun, T -> right);
    }

    void clear() {
      postorder([](node *t) -> void {delete t;}, root);
    }

    void insert(int i) {
      root = realInsert(i, root);
    }
    bool remove(int i) {
      if (!root)
        return false;
      return realRemove(root, i);
    }

    //测试函数
    void printPreorder() {
      preorder([](node *t) -> void {cout << t -> ele << "  ";}, root);
    }
    void printInorder() {
      inorder([](node *t) -> void {cout << t -> ele << "  ";}, root);
    }
    bool check() {
      queue<node*> bfs;
      if (root == 0)
        return true;
      bfs.push(root);
      while (bfs.size()) {
        node* t = bfs.front();
        bfs.pop();
        if ((height(t -> left) - height(t -> right) >= 2) || 
         (height(t -> left) - height(t -> right) <= -2))
          return false;
        if ((height(t) != height(t -> left) + 1) && (height(t) != height(t -> right) + 1))
          return false;
        if ((t -> left && (t -> ele < t -> left -> ele)) || (t -> right && (t -> ele > t -> right -> ele)))
          return false;
        if (t -> left)
          bfs.push(t -> left);
        if (t -> right)
          bfs.push(t -> right);
      }
      return true;
    }
    bool isRemoved(int i, vector<int> nodes) {
      node *temp = root;
      if (root == 0)
        return true;
      for (int index = 0; index < nodes.size(); ++index) {
        while (temp && temp -> ele != nodes[index]) {
          if (nodes[index] < temp -> ele)
            temp = temp -> left;
          else 
            temp = temp -> right;
        }
        if ((temp && temp -> ele == i) || (temp == 0 && nodes[index] != i))
          return false;
        temp = root;
      }
      return true;
    }
};


bool test() {
  int count = 0;
  avl test;
  default_random_engine e(time(0));
  uniform_int_distribution<unsigned> u(0,TEST_SIZE);
  vector<int> ran;
  for (int i = 0; i < TEST_SIZE; ++i) {
    int num = u(e);
    test.insert(num);
    if (find(ran.begin(), ran.end(), num) == ran.end())
      ran.push_back(num);
    if (test.check() == false)
      return false;
  }
  test.printPreorder();
  cout << endl;
  test.printInorder();
  cout << '\n' << ran.size() << endl;
  e.seed(time(0) + 1);
  while (ran.size() != 0) {
    uniform_int_distribution<unsigned> u1(0,ran.size() - 1);
    int index = u1(e);
    if (test.remove(ran[index]))
      ++count;
    if (test.check() == false || test.isRemoved(ran[index], ran) == false)
      return false;
    ran.erase(ran.begin() + index);
  }
  test.printInorder();
  cout << endl;
  test.printPreorder();
  cout << '\n' << count << endl;
  return true;
}

int main() {
  cout << test() << endl;
  return 0;
}
```
---

## 结语
- 其实插入操作很早就写好了（因为书上已经有实现代码了，但是删除操作是最近才写好的，大概经历了不知道怎么删除->删除之后怎么更新高度和重新平衡->debug的阶段，也复习了下二叉树的删除操作。
- 不得不说好的函数名是多么重要，刚开始我为旋转的命名是按照书上的命名（如`singleRotateWithLeft()`)，时常要重看代码区分哪个是哪个，后来改叫`leftleft()`后简单易懂，悔不当初啊
- 自己写的test代码也是bug多多，没测出树的bug倒是debug了挺久的test代码。
- 本来对双旋转也理解的不是很深，在写这篇博客时突然想到可以理解为化简为单旋转的情况。
- 因为偷懒所以把实现都写到了类里，其实应该把类的定义写在.h文件里，把方法实现写在单独的.cpp里。

最后照例贴*Reference*，AVL树是学习*《数据结构与算法分析——C语言描述》,Mark Allen Weiss*的，图是从[https://visualgo.net/en](https://visualgo.net/en)截图的，然后自己加上了辣眼睛的箭头，另外，二叉树删除的操作参考了[jarun/dslib/](https://github.com/jarun/dslib/blob/master/src/avl.c)