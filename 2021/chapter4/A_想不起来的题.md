## 高楼扔鸡蛋【困难】

**动态规划**

力扣地址：https://leetcode-cn.com/problems/super-egg-drop/submissions/



## 戳气球【困难】

**动态规划**

> 力扣地址：https://leetcode-cn.com/problems/burst-balloons/

方法一：回溯法【超时】

应该用动态规划



## 二叉树的序列化和反序列化

https://github.com/wxler/DailyAlgorithms/blob/master/2021/chapter3/5.16_SerializeBTree.md



github

**注意：反序列化时不能用栈，一定要用队列**

```java
	//先序遍历的反序列化
	public static Node reverseFirstOrder(String str){
		String[] arr=str.split("!");
		if(arr.length<=0) return null;
		Queue<String> queue=new LinkedList<>();
		for(String s:arr){
			queue.offer(s);
		}
		return reverseFirst(queue);
	}

	public static Node reverseFirst(Queue<String> queue){
		if(queue.isEmpty()) return null;
		String str=queue.poll();
		if("#".equals(str)){
			return null;
		}
		Node head=new Node(Integer.parseInt(str));
		head.left=reverseFirst(queue);
		head.right=reverseFirst(queue);
		return head;
	}

	public static Node createNode(String str){
		if("#".equals(str)){
			return null;
		}else{
			return new Node(Integer.parseInt(str));
		}
	}


	//层序遍历的反序列化
	public static Node reverseLevelOrder(String str){
		String[] arr=str.split("!");
		if(arr.length<=0) return null;

		int index=0;
		Node head=createNode(arr[index++]);
		if(head==null) return null;

		Queue<Node> queue=new LinkedList<>(); //不要用栈，一定要用队列
		queue.offer(head);
		while(!queue.isEmpty()){
			Node cur=queue.poll();
			if(index<arr.length) cur.left=createNode(arr[index++]);
			if(index<arr.length) cur.right=createNode(arr[index++]);
			if(cur.left!=null) queue.offer(cur.left);
			if(cur.right!=null) queue.offer(cur.right);
			
		}
		
		return head;
	}
```



## CD167 找到二叉树中符合搜索二叉树条件的最大拓扑结构

https://www.nowcoder.com/practice/e13bceaca5b14860b83cbcc4912c5d4a?tpId=101&tqId=33235&rp=1&ru=%2Fta%2Fprogrammer-code-interview-guide&qru=%2Fta%2Fprogrammer-code-interview-guide%2Fquestion-ranking&tab=answerKey



github

```java

import java.util.Scanner;

public class Main{
    
    static class Node{
        int value;
        Node left,right;
        public Node(int value){
            this.value=value;
        }
    }

    static int max=0;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n=sc.nextInt();
        int root=sc.nextInt();
        Node[] arr=new Node[n+1];
        Node node;
        for(int i=1;i<=n;i++){
            node=new Node(i);
            arr[i]=node;
        }
        int value,left,right;
        for(int i=1;i<=n;i++){
            value=sc.nextInt();
            left=sc.nextInt();
            right=sc.nextInt();
            arr[value].left=left==0?null:arr[left];
            arr[value].right=right==0?null:arr[right];
        }
        Node head=arr[root];
        getMax(head);
        System.out.println(max);
    }
    
//     public static int getMax(Node head){
//         if(head==null) return 0;
//         int r=getBSTCount(head);
//         int r1=getMax(head.left);
//         int r2=getMax(head.right);
//         return Math.max(r,Math.max(r1,r2));
//     }
    
    public static void getMax(Node head){
        if(head==null) return ;
        int r=getBSTCount(head);
        max=Math.max(max,r);
        getMax(head.left);
        getMax(head.right);
    }
    
    //这个计算BST个数的方法不对，因为只能判断相邻的大小，不能判断所有拓扑结构中的结点满足BST的特性
    //看左神P124
    public static int getBSTCount(Node head){
        if(head==null) return 0;
        if(head.left==null && head.right==null) return 1;
        
        int count=1;
        if(head.left!=null && head.value>head.left.value){
            count+=getBSTCount(head.left);
        }
        if(head.right!=null && head.value<head.right.value){
            count+=getBSTCount(head.right);
        }
        return count;
        
    }
    
    
}
```

如下图，用getBSTCount这个函数，3就被统计进去了。

![image-20210803150402883](A_%E6%83%B3%E4%B8%8D%E8%B5%B7%E6%9D%A5%E7%9A%84%E9%A2%98.assets/image-20210803150402883.png)

有一种方法可以统计BST拓扑结构的个数，比如统计以5为头结点的拓扑结构的个数，把5固定住，**按照BST的特性**，依次查找5、4、8、3，如果能查找到就把计数+1，如果查到不到，**该结点及其子节点都不用查找了**。

比如查到3，因为3<5，所以应该在5的左子树中查找，一定查找不到，所以不会统计3。

解法如下：

```java

import java.util.Scanner;

public class Main{
    
    static class Node{
        int value;
        Node left,right;
        public Node(int value){
            this.value=value;
        }
    }

    static int max=0;
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int n=sc.nextInt();
        int root=sc.nextInt();
        Node[] arr=new Node[n+1];
        Node node;
        for(int i=1;i<=n;i++){
            node=new Node(i);
            arr[i]=node;
        }
        int value,left,right;
        for(int i=1;i<=n;i++){
            value=sc.nextInt();
            left=sc.nextInt();
            right=sc.nextInt();
            arr[value].left=left==0?null:arr[left];
            arr[value].right=right==0?null:arr[right];
        }
        Node head=arr[root];
//         getMax(head);
//         System.out.println(max);
        System.out.println(getMax2(head));
    }
    //getMax2和getMax都可
    public static int getMax2(Node head){
        if(head==null) return 0;
        int r=getBSTCount(head,head);
        int r1=getMax2(head.left);
        int r2=getMax2(head.right);
        return Math.max(r,Math.max(r1,r2));
    }
    
    //getMax2和getMax都可
    public static void getMax(Node head){
        if(head==null) return ;
        int r=getBSTCount(head,head);
        max=Math.max(max,r);
        getMax(head.left);
        getMax(head.right);
    }
    
    //得到以head为头结点的BST拓扑结构个数，node为当前遍历的结点，head固定住
    //初始head和node一样
    public static int getBSTCount(Node head,Node node){
        if(node==null) return 0;
        int count=1;
        if(!judgeNode(head,node)) return 0;
        else{
            count=count+getBSTCount(head,node.left)+getBSTCount(head,node.right);
        }
        return count;
        
    }
    
    public static boolean judgeNode(Node head,Node node){
        if(head==node) return true;
        if(head==null) return false;
        if(node.value>head.value){
            return judgeNode(head.right,node);
        }else return judgeNode(head.left,node);
    }
    
}
```



