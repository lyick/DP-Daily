# Leyni的机器人

#### 问题描述

Leyni喜欢机器人，这次，他得到了一个机器人，这个机器人能接受两种命令，"T"（向后转），"F"（前进一步）。他还得到了一个用于控制这个机器人的指令序列，他计划恰好修改_k_次这个指令序列（一次只能修改其中一个指令，同一个指令也可以被反复修改）后让机器人去执行，并使这个机器人最终停下的位置离起点尽量远。他想知道这个机器人最终停下的位置离起点的最远距离，请你帮助他！

#### 输入

 一个长度不超过100的仅由"T"，"F"组成的非空字符串_s_代表着原始指令序列。

 一个整数_k_ \(1 ≤ _k_ ≤ 50\)代表着Leyni计划恰好修好这个指令序列的次数。

#### 输出

机器人最终停下的位置离起点的最远距离。

#### 样例

```text
Input: "FTF" 1 , Output: 3
```

#### 思路

> 考虑机器人行动的两个方向，假设初始方向为正向，表示为1，则转向后方向为0。
>
> 为了得到最终最优结果，需要记录每一步操作时的最优结果然后进行状态转换。
>
> 定义dp\_max\[i\]\[j\]\[p\]为进行到第i个指令时，修改指令j次，方向为p的距离最大值。
>
> 考虑到最终的方向可能与开始方向相反，那么中途需要让开始方向行走的距离尽量小，因此记录另一个值dp\_min\[i\]\[j\]\[p\]为距离最小值。
>
> 然后考虑状态转移。
>
> 当进行第i+1个指令时，遍历进行第i个指令在不同修改次数时的记录。设修改次数为n。
>
> 若第i+1个指令为F，设到第i+1个指令时修改次数为m，则当 \(m-n\)整除2时，指令未变。
>
> 更新值:
>
>  dp\_max\[i+1\]\[m\]\[p\] = max\(dp\_max\[i+1\]\[m\]\[p\]， dp\_max\[i\]\[n\]\[p\] + p==0? 1: -1 \)
>
>  dp\_min\[i+1\]\[m\]\[p\] = min\(dp\_min\[i+1\]\[m\]\[p\]， dp\_min\[i\]\[n\]\[p\] + p==0? 1: -1 \)
>
> 当（m-n）不整除2时，此时指令变为T，转向，不同方向的记录交换。
>
>  dp\_max\[i+1\]\[m\]\[p\] = max\(dp\_max\[i+1\]\[m\]\[p\]， dp\_max\[i\]\[n\]\[1-p\]\) 
>
>  dp\_min\[i+1\]\[m\]\[p\] = min\(dp\_min\[i+1\]\[m\]\[p\]， dp\_min\[i\]\[n\]\[1-p\]\)
>
> 第i+1个指令为T时，思路与上类似，转换相反。
>
> 最初第一个指令时各记录的值可以直接得到，利用循环可以逐步获取进一步的记录，最终比较到最后一个指令时各向最大值。
>
> 考虑到指令长度小于100，最大值的记录初始值可设置为-101，最小值设为101

#### 代码样例 \(Java\)

```java
int LongestDistance(String s, int k){
    int l = s.length;
    int dp_max = new int[100][50][2];
    int dp_min = new int[100][50][2];
    Arrays.fill(dp_max, -101);
    Arrays.fill(dp_min, 101);
    for(int j = 0; j < k; j++){
        if(j % 2 == 0)
            if(s.chatAt(0) == 'F'){
                dp_max[0][j][0] = 1;
                dp_min[0][j][0] = 1;
            } 
            else {
                dp_max[0][j][1] = 0;
                dp_min[0][j][1] = 0;
            }
        else
            if(s.chatAt(0) == 'F'){
                dp_max[0][j][1] = 0;
                dp_min[0][j][1] = 0;
            } 
            else {
                dp_max[0][j][0] = 1;
                dp_min[0][j][0] = 1;
            }
    }
    for(int i = 1; i < l; i++){
        for(int m = 0; m < k; m++){
            for(int n = 0; n < m; n++){
                if((m - n) % 2 == 0){
                    if(s.charAt(i) == 'F'){
                        dp_max[i][m][0] = max(dp_max[i][m][0], dp_max[i - 1][m][0] - 1);
                        dp_max[i][m][1] = max(dp_max[i][m][0], dp_max[i - 1][m][1] + 1);
                        dp_min[i][m][0] = min(dp_min[i][m][0], dp_min[i - 1][m][0] - 1);
                        dp_min[i][m][1] = min(dp_min[i][m][0], dp_min[i - 1][m][1] + 1);
                    }
                    else {
                        dp_max[i][m][0] = max(dp_max[i][m][0], dp_max[i - 1][m][1]);
                        dp_max[i][m][1] = max(dp_max[i][m][0], dp_max[i - 1][m][0]);
                        dp_min[i][m][0] = min(dp_min[i][m][0], dp_min[i - 1][m][1]);
                        dp_min[i][m][1] = min(dp_min[i][m][0], dp_min[i - 1][m][0]);
                    }
                }
                else {
                    if(s.charAt(i) == 'F'){
                        dp_max[i][m][0] = max(dp_max[i][m][0], dp_max[i - 1][m][1]);
                        dp_max[i][m][1] = max(dp_max[i][m][0], dp_max[i - 1][m][0]);
                        dp_min[i][m][0] = min(dp_min[i][m][0], dp_min[i - 1][m][1]);
                        dp_min[i][m][1] = min(dp_min[i][m][0], dp_min[i - 1][m][0]);
                    }
                    else {
                        dp_max[i][m][0] = max(dp_max[i][m][0], dp_max[i - 1][m][0] - 1);
                        dp_max[i][m][1] = max(dp_max[i][m][0], dp_max[i - 1][m][1] + 1);
                        dp_min[i][m][0] = min(dp_min[i][m][0], dp_min[i - 1][m][0] - 1);
                        dp_min[i][m][1] = min(dp_min[i][m][0], dp_min[i - 1][m][1] + 1);
                    }
                }
            }
        }
    }
    return max(dp_max[l - 1][k - 1][1], dp_max[l - 1][k - 1][0]);
}
//代码仅作思路示例，不保证运行
```

