![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

       前期写代码的时候都会困惑这个实际的threadIdx（tid，实际的线程id）到底是多少，自己写出来的对不对，今天经过自己一些小例子的推敲，以及找到官网的相关介绍，总算自己弄清楚了。

      在启动kernel的时候，要通过指定gridsize和blocksize才行，举下面的例子说说：

     dim3 gridsize(2,2);

     dim3 blocksize(4,4);

     gridsize相当于是一个2\*2的block，gridDim.x，gridDim.y，gridDim.z相当于这个dim3的x，y，z方向的维度，这里是2\*2\*1。序号从0到3，且是从上到下的顺序，就是说是下面的情况:

     grid中的blockidx序号标注情况为：            0     2 

                                                                        1     3

    blocksize则是指里面的thread的情况，blockDim.x，blockDim.y，blockDim.z相当于这个dim3的x，y，z方向的维度，这里是4\*4\*1.序号是0-15，也是从上到下的标注：

    block中的threadidx序号标注情况：          0      4       8      12 

                                                                      1       5       9       13

                                                                      2       6       10     14

                                                                      3       7       11      15

  应该这样子就一目了然了，然后求实际的tid的时候：

![](https://img-blog.csdn.net/20160809143848708?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  

![](https://img-blog.csdn.net/20160809143858896?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  

最后还发现了一个2D \* 2D 表示threadid的二维位置的标示图， 适用于将每个threadid跟矩阵中的二维元素进行一一对应。

![](https://img-blog.csdn.net/20160809145945472)
  

![](https://img-blog.csdn.net/20160809150525718)