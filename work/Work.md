 **��������������**

����b2b��Ŀ��ʱ����Ҫ��Debug Configuration��ǩ ��� VM options����Ϊ��
>-server -Xverify:none -Xms1024M -Xmx2048M -XX:+UseParNewGC -XX:PermSize=128m -ea

�ҵ����ϲ����Ľ������£�

-server ʹ��server jvm������ʹ�ã���Щdoc˵IDEA�Ӹ�ѡ���������ٶȡ� 
-Xverify��none �ر�Java�ֽ�����֤���Ӷ��ӿ�����װ����ٶȣ���ʹ���ڽ�Ϊ��֤Ŀ�Ķ������Ĺ���������װ���࣬����������ʱ�䡣

Xms1024m ������ڴ��ʼֵΪ1024MB
Xmx2048m ������ڴ����ֵΪ2048MB

-XX:+UseParNewGC��
ʹ��-XX:+UseParNewGCѡ����������������ʹ��ParNew GC���ԣ����ϴ�Ĭ��ʹ��Serial Old GC���ԡ�
ParNew�ռ�����ʵ����Serial�ռ����Ķ��̰߳汾��**���������У���������У������������㷨����������-ѹ��**

-XX:PermSize=128m
JVMʹ��-XX:PermSize���÷Ƕ��ڴ��ʼֵ��Ĭ���������ڴ��1/64����XX:MaxPermSize�������Ƕ��ڴ�Ĵ�С��Ĭ���������ڴ��1/4��

���������jvm����������Ͽ���
>ʹ��ParNew��Serial�ȴ���Compact���̵��ռ���ʱ��ϵͳʹ�õķ����㷨��ָ����ײ������Java�����ڴ���Թ����������ù����ڴӷŵ�һ�ߣ����еķŵ���һ�ߣ��м����һ��ָ����Ϊ�ֽ���ָʾ�������������ڴ���ǽ�����ָ������еĿռ��Ǳ�Ų��һ�δ���������С��ȵľ��룩���������ʹ��CMS���ֻ���Mark-Sweep�㷨���ռ���ʱ��ϵͳ���ÿ����б���䷽ʽ�������ڴ沢����������ʹ�úͿ����ڴ��໥����������ͱ���ά��һ���б���¼����Щ�ڴ���ǿ��õģ��ٷ���ʱ���б����ҵ�һ���㹻��Ŀռ仮�ָ�����ʵ�����������б��ϵ�ʵ������


�������ұ���b2b�ķ���jvm����

�����ǲ��Ի��������ã�
>JAVA_OPTS=-server -Xms5120M -Xmx5120M -Xmn512M -Xss256k 
-XX:+AggressiveOpts -XX:+UseBiasedLocking -XX:PermSize=1024M 
-XX:MaxPermSize=1024M -XX:+DisableExplicitGC -XX:MaxTenuringThreshold=31 
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+CMSParallelRemarkEnabled 
-XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128M 
-XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly

-Xss256k ջ��������

-XX:+AggressiveOpts	�ӿ����

-XX:+UseBiasedLocking	�����Ƶ����ܸ���

-XX:+DisableExplicitGC	�ر�System.gc()	 	���������Ҫ�ϸ�Ĳ���

-XX:MaxTenuringThreshold	�����������	 	�������Ϊ0�Ļ�,����������󲻾���Survivor��,ֱ�ӽ������ϴ�. �������ϴ��Ƚ϶��Ӧ��,�������Ч��.�������ֵ����Ϊһ���ϴ�ֵ,��������������Survivor�����ж�θ���,�����������Ӷ�����������Ĵ��ʱ��,������������������յĸ���
�ò���ֻ���ڴ���GCʱ����Ч.

-XX:+UseConcMarkSweepGC	ʹ��CMS�ڴ��ռ�	 	��������������Ժ�,-XX:NewRatio=4������ʧЧ��,ԭ����.����,��ʱ�������С�����-Xmn����.???  �����ϴ�

-XX:+CMSParallelRemarkEnabled	���ͱ��ͣ��

-XX:+UseCMSCompactAtFullCollection:ʹ�ò����ռ���ʱ,���������ϴ���ѹ��.

-XX:LargePageSizeInBytes	�ڴ�ҳ�Ĵ�С�������ù��� ��Ӱ��Perm�Ĵ�С	 	=128m

-XX:+UseFastAccessorMethods	ԭʼ���͵Ŀ����Ż�

-XX:+UseCMSInitiatingOccupancyOnly	ʹ���ֶ������ʼ�����忪ʼCMS�ռ�	 	��ֹhostspot���д���CMS GC


����������������һ������Ȩ�޵����⣬����һ��mysql��ǩ��������ʱ������һ��

��������idea����ڷ������ϴ�ϵ㣬�����ͻ��ر�����



    <tx:advice id="txAdvice" transaction-manager="transactionManager">
       <tx:attributes>
             <tx:method name="get*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="find*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="search*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="list*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="is*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="has*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
		</tx:attributes>
    </tx:advice>

    <aop:config proxy-target-class="true">
        <aop:pointcut id="interceptorPointCuts"
            expression="execution(* com.b2bex.*.service.*Manager.*(..)) or execution(* com.autozi.*.*.service.*Manager.*(..))" />
        <aop:advisor advice-ref="txAdvice"
            pointcut-ref="interceptorPointCuts" />
    </aop:config>
    
��Ŀ������ͨ��interceptorPointCuts��manager��ķ�������������ع�������

�����forѭ��Ȼ��update���� д����mangager�㵼�� ����Ƕ������ �������еĸ��²�������ʧЧ�����ع���û���ύ�ˡ�