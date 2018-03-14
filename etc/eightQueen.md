##问题来源量子位楼教主的问题
![Image text](./eightQueen.jpg)

八皇后问题，是一个古老而著名的问题，是回溯算法的典型案例。该问题是国际西洋棋棋手马克斯·贝瑟尔于1848年提出：在8×8格的国际象棋上摆放八个皇后，使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。 高斯认为有76种方案。1854年在柏林的象棋杂志上不同的作者发表了40种不同的解，后来有人用图论的方法解出92种结果。

—-以上内容引自百度百科。

本程序的功能是打印八皇后的所有摆放方式，用回溯法解决，使用的语言是Java，主要函数在代码中都有注释。

主要思想是：从第0行开始摆放皇后，第0行摆放完毕后，就摆放第1行，以此类推…当第7行摆放完毕，即棋盘上已经有了8个皇后，就打印出所有皇后的路径。接下来，就从第8个皇后开始，逐一将摆放好的皇后撤回。每撤回一个，就试探该行的其它位置（因为我们要打印所有正确的摆法）。

程序如下：

从运行结果可以看出：八皇后共有92种摆法。
16皇后共有 14772512种摆放方式

    public class EightQueen{

    private static final int SIZE = 8;  //皇后的个数，此处设为8，表示8个皇后

    private static int count = 0;  //记录摆放的方式数

    public static void main(String[] args) {

        LinkedList<Location> list = new LinkedList<Location>();

        eightQueen(list, 0, 0);  //从棋盘的第0行第0列开始

        System.out.println("八皇后共有 " + count + "种摆放方式");

    }

    /**
     * 定义位置的数列结构，用于表示皇后的摆放位置
     */
    static class Location {
        int x ;  //对应棋盘的列
        int y ;  //对应棋盘的行

        Location(int x, int y){
            this.x = x;
            this.y = y;
        }

        @Override
        public String toString() {
            return "(" + x + ", " + y + ")";
        }
    }

    /**
     * 主要函数，用回溯法。
     */
    private static void eightQueen(LinkedList<Location> list, int x, int y) {   

        if(list.size() == SIZE){  //当list元素个数为8时，表示8个皇后都摆放完毕，打印后即可退出函数。
            printLocation(list);  //打印皇后摆放方式
            return ;
        }

        for(int i = x ; i < SIZE ; i++){
            Location loc = new Location(i, y);
            if(isLegalLoc(list, loc)){
                list.offer(loc);  //将第y行的皇后摆放好
                eightQueen(list, 0, y+1);  //开始摆放y+1行的皇后，同样从第0列开始摆放
                list.pollLast();  //每次摆放完一个皇后后，都要将其撤回，再试探其它的摆法。
            }                   
        }           
    }

    /**
     * 判断位置为loc的皇后是否合法
     */
    private static boolean isLegalLoc(LinkedList<Location> list, Location loc) {
        for(Location each : list){
            if(loc.x == each.x || loc.y == each.y)  //判断是否在同一行或同一列
                return false;
            else if (Math.abs(loc.x - each.x) == Math.abs(loc.y - each.y))  //判断是否在同一对角线或反对角线上
                return false;
        }
        return true;
    }

    /**
     * 打印皇后摆放方式
     * @param list
     */
    private static void printLocation(LinkedList<Location> list) {
        for(Location each : list){
            System.out.print(each.toString() + "\t");
        }
        System.out.println();
        count ++;
    }
    }


————————————————————————————

第二种方案： 

>1.简单介绍回溯法思路，就是将所有的结果变成一棵树，从树的结点开始访问，采用深度优先策略，从树的根结点开始访问，如果满足条件，继续访问下一层，如果不满足条件，返回上一个结点，继续访问其它结点。重复操作。 
2. 对于N皇后问题，我特意做了一张图片。首先放置第一列，有四种放法，如果第一列，放置在第一个，再放置第二列，也有四种放法，很显然，第二列的第一种放法，不符合条件，这种放法下面所的子树也就不必访问了，节省了访问时间，然后继续第三列，第四列。就可以得到结果了。 

    package com.bc;

    public class huanghou {

    public static int Q=4; //代表四皇后

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        int[][] dp=new int[Q][Q];
        int i,j;
        //初始化
        for(i=0;i<Q;i++)
        {
            for(j=0;j<Q;j++)
            {
                dp[i][j]=0;
            }
        }
        que(0,dp);
    }

    private static void que(int m, int[][] dp) {
        // TODO Auto-generated method stub

        if(m==Q){//递归结束条件

          for(int i=0;i<Q;i++)
          {    
              for(int j=0;j<Q;j++)
              {
                  System.out.print(dp[i][j]+" ");
              }
              System.out.println("\n");
          }
          System.out.println("**********************");
        }
        //递归计算
        for(int i=0;i<Q;i++)
        {
            if (isCorrt(i,m,dp)) {
               dp[i][m]=1;  
               que(m+1, dp);
               dp[i][m]=0;
            }
        }
    }
    //判断这个位置能不能放皇后
    private static boolean isCorrt(int i, int j, int[][] dp) {
        // TODO Auto-generated method stub
            int s, t;  //s代表行,t代表列 
            for(s=i,t=0; t<Q; t++)
                if(dp[s][t]==1 && t!=j)
                    return false;//判断行
            for(t=j,s=0; s<Q; s++)
                if(dp[s][t]==1 && s!=i)
                    return false;//判断列
            for(s=i-1,t=j-1; s>=0&&t>=0; s--,t--)
                if(dp[s][t]==1)
                    return false;//判断左上方
            for(s=i+1,t=j+1; s<Q&&t<Q;s++,t++)
                if(dp[s][t]==1)
                    return false;//判断右下方
            for(s=i-1,t=j+1; s>=0&&t<Q; s--,t++)
                if(dp[s][t]==1)
                    return false;//判断右上方
            for(s=i+1,t=j-1; s<Q&&t>=0; s++,t--)
                if(dp[s][t]==1)
                    return false;//判断左下方
            return true;
    }

    }