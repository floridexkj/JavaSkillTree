##问题来源量子位楼教主的问题
![Image text](./eightQueen.jpg)

八皇后问题，是一个古老而著名的问题，是回溯算法的典型案例。该问题是国际西洋棋棋手马克斯·贝瑟尔于1848年提出：在8×8格的国际象棋上摆放八个皇后，使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。 高斯认为有76种方案。1854年在柏林的象棋杂志上不同的作者发表了40种不同的解，后来有人用图论的方法解出92种结果。

—-以上内容引自百度百科。

本程序的功能是打印八皇后的所有摆放方式，用回溯法解决，使用的语言是Java，主要函数在代码中都有注释。

主要思想是：从第0行开始摆放皇后，第0行摆放完毕后，就摆放第1行，以此类推…当第7行摆放完毕，即棋盘上已经有了8个皇后，就打印出所有皇后的路径。接下来，就从第8个皇后开始，逐一将摆放好的皇后撤回。每撤回一个，就试探该行的其它位置（因为我们要打印所有正确的摆法）。

程序如下：

从运行结果可以看出：八皇后共有92种摆法。


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