

BFS一般用来求最短距离，DFS其实就是回溯算法（写递归用）



## 二叉树的最小高度

力扣地址：https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/



先来最平常的递归算法

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null) return 0;
        return getMinDeep(root);

    }

    public int getMinDeep(TreeNode root){
        if(root.left==null && root.right==null) return 1;
        
        int deep=Integer.MAX_VALUE;
        if(root.left!=null)
            deep=Math.min(deep,1+minDepth(root.left)); //这里，调用了minDepth
        if(root.right!=null)
            deep=Math.min(deep,1+minDepth(root.right));
        return deep;
    }
}
```

递归方法，有点小改动：

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null) return 0;

        return getMinDeep(root);
    }

    public int getMinDeep(TreeNode root){

        if(root.left==null && root.right==null){
            return 1;
        }
        int left=Integer.MAX_VALUE;
        int right=Integer.MAX_VALUE;
        if(root.left!=null) left=1+getMinDeep(root.left); //这里，调用了自己，因为我们已经判断root.left不为空了，所以不用再用minDepth里面的判空条件了
        if(root.right!=null) right=1+getMinDeep(root.right);
        return Math.min(left,right);

    }
}
```





用BFS求最小深度

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null) return 0;
        Queue<TreeNode> queue=new LinkedList<>();
        queue.offer(root);
        int deep=1;
        while(!queue.isEmpty()){
            int sz=queue.size();
            for(int i=0;i<sz;i++){
                TreeNode node=queue.poll();
                if(node.left==null && node.right==null)
                    return deep;
                if(node.left!=null) queue.offer(node.left);
                if(node.right!=null) queue.offer(node.right);
            }
            deep++;
        }
        return deep;

    }
}
```



BFS比DFS效率更高，但空间用的比DFS多。



## 打开转盘锁【中等】

力扣地址：https://leetcode-cn.com/problems/open-the-lock/



方法一：单向BFS（超时）

```java
class Solution {

    public String plusOne(String str,int k){
        char[] chars=str.toCharArray();
        if(chars[k]=='9')
            chars[k]='0';
        else chars[k]=(char)(chars[k]+1);//或chars[k]+=1;
        return new String(chars);
    }

    public String minusOne(String str,int k){
        char[] chars=str.toCharArray();
        if(chars[k]=='0')
            chars[k]='9';
        else chars[k]=(char)(chars[k]-1);//或chars[k]-=1;
        return new String(chars);
    }

    public int openLock(String[] deadends, String target) {


        HashSet<String> deads=new HashSet<>();
        for(String s:deadends)
            deads.add(s);
        
        if(deads.contains("0000")) return -1;//排除极端情况

        Queue<String> queue=new LinkedList<>();
        HashSet<String> visitead=new HashSet<>();
        queue.offer("0000");
        visitead.add("0000");

        int step=0;

        while(!queue.isEmpty()){
            int sz=queue.size();
            for(int i=0;i<sz;i++){
                String str=queue.poll();
                if(str.equals(target))
                    return step;
                
                for(int k=0;k<4;k++){
                    String s1=plusOne(str,k);
                    String s2=minusOne(str,k);
                    //!deads.contains(s1)：就不让deads中的几点进
                    if(!visitead.contains(s1)&&!deads.contains(s1)){
                        queue.offer(s1);
                        visitead.add(s1); //这个不要忘记了
                    }
                        
                    if(!visitead.contains(s2)&&!deads.contains(s2))
                        queue.offer(s2);
                        visitead.add(s2);
                }
            }
            step++;
            
        }

        return -1;
    }
}
```

简单修改一下：

```java
class Solution {

    public String plusOne(String str,int k){
        char[] chars=str.toCharArray();
        if(chars[k]=='9')
            chars[k]='0';
        else chars[k]=(char)(chars[k]+1);//或chars[k]+=1;
        return new String(chars);
    }

    public String minusOne(String str,int k){
        char[] chars=str.toCharArray();
        if(chars[k]=='0')
            chars[k]='9';
        else chars[k]=(char)(chars[k]-1);//或chars[k]-=1;
        return new String(chars);
    }

    public int openLock(String[] deadends, String target) {


        HashSet<String> deads=new HashSet<>();
        for(String s:deadends)
            deads.add(s);
        

        Queue<String> queue=new LinkedList<>();
        HashSet<String> visitead=new HashSet<>();
        queue.offer("0000");
        visitead.add("0000");

        int step=0;

        while(!queue.isEmpty()){
            int sz=queue.size();
            for(int i=0;i<sz;i++){
                String str=queue.poll();

                if(deads.contains(str)) continue;//deads中的结点不起作用

                if(str.equals(target))
                    return step;
                
                for(int k=0;k<4;k++){
                    String s1=plusOne(str,k);
                    String s2=minusOne(str,k);
                    if(!visitead.contains(s1)){
                        queue.offer(s1);
                        visitead.add(s1);
                    }
                        
                    if(!visitead.contains(s2))
                        queue.offer(s2);
                        visitead.add(s2);
                }
            }
            step++;
            
        }

        return -1;
    }
}
```







