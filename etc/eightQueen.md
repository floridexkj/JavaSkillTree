##������Դ����λ¥����������
![Image text](./eightQueen.jpg)

�˻ʺ����⣬��һ�����϶����������⣬�ǻ����㷨�ĵ��Ͱ������������ǹ����������������˹����ɪ����1848���������8��8��Ĺ��������ϰڷŰ˸��ʺ�ʹ�䲻�ܻ��๥���������������ʺ󶼲��ܴ���ͬһ�С�ͬһ�л�ͬһб���ϣ����ж����ְڷ��� ��˹��Ϊ��76�ַ�����1854���ڰ��ֵ�������־�ϲ�ͬ�����߷�����40�ֲ�ͬ�Ľ⣬����������ͼ�۵ķ������92�ֽ����

��-�����������԰ٶȰٿơ�

������Ĺ����Ǵ�ӡ�˻ʺ�����аڷŷ�ʽ���û��ݷ������ʹ�õ�������Java����Ҫ�����ڴ����ж���ע�͡�

��Ҫ˼���ǣ��ӵ�0�п�ʼ�ڷŻʺ󣬵�0�аڷ���Ϻ󣬾Ͱڷŵ�1�У��Դ����ơ�����7�аڷ���ϣ����������Ѿ�����8���ʺ󣬾ʹ�ӡ�����лʺ��·�������������ʹӵ�8���ʺ�ʼ����һ���ڷźõĻʺ󳷻ء�ÿ����һ��������̽���е�����λ�ã���Ϊ����Ҫ��ӡ������ȷ�İڷ�����

�������£�

�����н�����Կ������˻ʺ���92�ְڷ���
16�ʺ��� 14772512�ְڷŷ�ʽ

    public class EightQueen{

    private static final int SIZE = 8;  //�ʺ�ĸ������˴���Ϊ8����ʾ8���ʺ�

    private static int count = 0;  //��¼�ڷŵķ�ʽ��

    public static void main(String[] args) {

        LinkedList<Location> list = new LinkedList<Location>();

        eightQueen(list, 0, 0);  //�����̵ĵ�0�е�0�п�ʼ

        System.out.println("�˻ʺ��� " + count + "�ְڷŷ�ʽ");

    }

    /**
     * ����λ�õ����нṹ�����ڱ�ʾ�ʺ�İڷ�λ��
     */
    static class Location {
        int x ;  //��Ӧ���̵���
        int y ;  //��Ӧ���̵���

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
     * ��Ҫ�������û��ݷ���
     */
    private static void eightQueen(LinkedList<Location> list, int x, int y) {   

        if(list.size() == SIZE){  //��listԪ�ظ���Ϊ8ʱ����ʾ8���ʺ󶼰ڷ���ϣ���ӡ�󼴿��˳�������
            printLocation(list);  //��ӡ�ʺ�ڷŷ�ʽ
            return ;
        }

        for(int i = x ; i < SIZE ; i++){
            Location loc = new Location(i, y);
            if(isLegalLoc(list, loc)){
                list.offer(loc);  //����y�еĻʺ�ڷź�
                eightQueen(list, 0, y+1);  //��ʼ�ڷ�y+1�еĻʺ�ͬ���ӵ�0�п�ʼ�ڷ�
                list.pollLast();  //ÿ�ΰڷ���һ���ʺ�󣬶�Ҫ���䳷�أ�����̽�����İڷ���
            }                   
        }           
    }

    /**
     * �ж�λ��Ϊloc�Ļʺ��Ƿ�Ϸ�
     */
    private static boolean isLegalLoc(LinkedList<Location> list, Location loc) {
        for(Location each : list){
            if(loc.x == each.x || loc.y == each.y)  //�ж��Ƿ���ͬһ�л�ͬһ��
                return false;
            else if (Math.abs(loc.x - each.x) == Math.abs(loc.y - each.y))  //�ж��Ƿ���ͬһ�Խ��߻򷴶Խ�����
                return false;
        }
        return true;
    }

    /**
     * ��ӡ�ʺ�ڷŷ�ʽ
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


��������������������������������������������������������

�ڶ��ַ����� 

>1.�򵥽��ܻ��ݷ�˼·�����ǽ����еĽ�����һ�����������Ľ�㿪ʼ���ʣ�����������Ȳ��ԣ������ĸ���㿪ʼ���ʣ������������������������һ�㣬���������������������һ����㣬��������������㡣�ظ������� 
2. ����N�ʺ����⣬����������һ��ͼƬ�����ȷ��õ�һ�У������ַŷ��������һ�У������ڵ�һ�����ٷ��õڶ��У�Ҳ�����ַŷ�������Ȼ���ڶ��еĵ�һ�ַŷ������������������ַŷ�������������Ҳ�Ͳ��ط����ˣ���ʡ�˷���ʱ�䣬Ȼ����������У������С��Ϳ��Եõ�����ˡ� 

    package com.bc;

    public class huanghou {

    public static int Q=4; //�����Ļʺ�

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        int[][] dp=new int[Q][Q];
        int i,j;
        //��ʼ��
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

        if(m==Q){//�ݹ��������

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
        //�ݹ����
        for(int i=0;i<Q;i++)
        {
            if (isCorrt(i,m,dp)) {
               dp[i][m]=1;  
               que(m+1, dp);
               dp[i][m]=0;
            }
        }
    }
    //�ж����λ���ܲ��ܷŻʺ�
    private static boolean isCorrt(int i, int j, int[][] dp) {
        // TODO Auto-generated method stub
            int s, t;  //s������,t������ 
            for(s=i,t=0; t<Q; t++)
                if(dp[s][t]==1 && t!=j)
                    return false;//�ж���
            for(t=j,s=0; s<Q; s++)
                if(dp[s][t]==1 && s!=i)
                    return false;//�ж���
            for(s=i-1,t=j-1; s>=0&&t>=0; s--,t--)
                if(dp[s][t]==1)
                    return false;//�ж����Ϸ�
            for(s=i+1,t=j+1; s<Q&&t<Q;s++,t++)
                if(dp[s][t]==1)
                    return false;//�ж����·�
            for(s=i-1,t=j+1; s>=0&&t<Q; s--,t++)
                if(dp[s][t]==1)
                    return false;//�ж����Ϸ�
            for(s=i+1,t=j-1; s<Q&&t>=0; s++,t--)
                if(dp[s][t]==1)
                    return false;//�ж����·�
            return true;
    }

    }