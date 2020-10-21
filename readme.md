# 标签：树

树的定义：

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
```



## 94. 二叉树中序遍历

给定一个二叉树，返回它的*中序* 遍历。

递归遍历解法：

java形式：

```java
class Solution {
    public List<Integer> tmp = new ArrayList();
    public List<Integer> inorderTraversal(TreeNode root) {
        if(root == null) return tmp;
        inorderTraversal(root.left);
        tmp.add(root.val);
        inorderTraversal(root.right);
        return tmp;
        
    }
}
```

### 官方题解

递归解法：

思路和算法：

首先我们需要了解什么是二叉树的中序遍历：按照访问左子树——根节点——右子树的方式遍历这棵树，而在访问左子树或者右子树的时候我们按照同样的方式遍历，直到遍历完整棵树。因此整个遍历过程天然具有递归的性质，我们可以直接用递归函数来模拟这一过程。

```c++
class Solution {
public:
    void inorder(TreeNode* root, vector<int>& res) {
        if (!root) {
            return;
        }
        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root, res);
        return res;
    }
};
```

![](./img/Snipaste_2020-10-18_14-36-20.png)

## 144. 二叉树的前序遍历

递归java：

```java
class Solution {
    List<Integer> result = new ArrayList();
    public List<Integer> preorderTraversal(TreeNode root) {
        if(root == null) return result;
        result.add(root.val);
        preorderTraversal(root.left);
        preorderTraversal(root.right);

        return result;
    }
}
```

## 145. 二叉树的后序遍历

```java
class Solution {
    List<Integer> result = new ArrayList();
    public List<Integer> postorderTraversal(TreeNode root) {
        if(root == null) return result;
        postorderTraversal(root.left);
        postorderTraversal(root.right);
        result.add(root.val);

        return result;
    }
}
```

## 105. 从前序与中序遍历序列构造二叉树

前提：没有重复元素

本题的核心是要理解 ：

* 前序遍历时，根总出现在最前面，然后是左子树，然后是右子树
* 中序遍历时，左子树出现在最前面，然后是根，然后是右子树

![](./img/Snipaste_2020-10-18_15-39-30.png)

java：

```java
class Solution {
    Map<Integer,Integer> indexMap = new HashMap();
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        for(int i=0;i<n;i++){
            indexMap.put(inorder[i],i);	
        }
        /*
        	这里使用了散列表的目的是查找根结点在中序遍历序列中的下标时，让O(n)为O(1)常数级别。
        */
        return myTree(preorder,inorder,0,n-1,0,n-1);
    }
    public TreeNode myTree(int []preorder,int []inorder,int pre_start,int pre_end,int in_start,int in_end){
        if(pre_start > pre_end) return null;
        int pre_root = pre_start;   //获取根结点下标	因为前序遍历序列中的第一个元素应该是根结点
        int in_root = indexMap.get(preorder[pre_root]); //获取根结点在中序遍历结果中的下标

        int left_length = in_root - in_start; // 计算左子树长度
        int right_length = in_end - in_root; //计算右子树长度

        TreeNode root = new TreeNode(preorder[pre_root]);     //构造根节点
        root.left = myTree(preorder,inorder,pre_start+1,pre_start+left_length,in_start,in_root-1);
        //左子树在前序遍历中的范围为：[pre_start+1,pre_start+left_length]，即在当前pre_start后一位到长度为左子树长度的结点，在中序遍历中的范围为：[in_start,in_root-1]，即在in_start到根结点所在位置之前一位。
        root.right = myTree(preorder,inorder,pre_start+left_length+1,pre_end,in_root+1,in_end);
        return root;

    }

  
}
```



官方题解：

```java
class Solution {
    private Map<Integer, Integer> indexMap;

    public TreeNode myBuildTree(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return null;
        }

        // 前序遍历中的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = indexMap.get(preorder[preorder_root]);
        
        // 先把根节点建立出来
        TreeNode root = new TreeNode(preorder[preorder_root]);
        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;
        // 递归地构造左子树，并连接到根节点
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素
        root.left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root.right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        // 构造哈希映射，帮助我们快速定位根节点
        indexMap = new HashMap<Integer, Integer>();
        for (int i = 0; i < n; i++) {
            indexMap.put(inorder[i], i);
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }
}
```



## 106. 从中序和后序遍历序列构造二叉树

本题解题思路类似于从前序与中序遍历序列构造二叉树。

本题的核心是要理解 ：

* 中序遍历时，左子树出现在最前面，然后是根，然后是右子树
* 后序遍历时，左子树出现在最前面，然后是右子树，然后是根

java：

```java
class Solution {
    Map<Integer,Integer> indexMap = new HashMap();
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        int n = inorder.length;
        for(int i=0;i<n;i++){
            indexMap.put(inorder[i],i);
        }
        //构造树
        return myTree(inorder,postorder,0,n-1,0,n-1);
    }

    public TreeNode myTree(int []inorder,int []postorder,int in_start,int in_end,int post_start,int post_end){
        //返回条件
        if(post_start > post_end) return null;

        int post_root = post_end;
        int in_root = indexMap.get(postorder[post_root]);

        //构造根结点
        TreeNode root = new TreeNode(postorder[post_root]);
        //计算左子树长度：
        int left_length = in_root - in_start;
        //计算右子树长度：
        int right_length = in_end - in_root;

        root.left = myTree(inorder,postorder,in_start,in_root-1,post_start,post_start+left_length-1);
        root.right = myTree(inorder,postorder,in_root+1,in_end,post_end-right_length,post_end-1);
        return root;
        
    }
}
```

## 通过不同顺序遍历序列构造二叉树总结：

中序遍历序列是核心，我们通过在前序/后序遍历序列中找到根结点（最左或最右），然后根据这个根结点在中序序列中找到它的左右子树。然后递归这个过程，直到子序列长度为0.

**总结来说，构造二叉树的问题，本质上是一个二叉树的前序遍历问题。**

## 104. 二叉树的最大深度

要计算二叉树的最大深度，即找出左子树和右子树中最大深度+1即可，即$max(l,r)+1$。其中，`l`为左子树最大深度，`r`为右子树最大深度。而左子树/右子树的最大深度也可以通过同样的方式来计算。所以这就是一个递归的算法。

​	java：

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null){
            return 0;
        }else{
            int left = maxDepth(root.left);
            int right = maxDepth(root.right);
            return Math.max(left,right)+1;
        }
    }
}
```

本质来看，它的形式是后序遍历。

## 111. 二叉树的最小深度（*复习图后再来做）

Java：

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null){
            return 0;
        }else if(root.left==null){	//遇到右斜树
            return minDepth(root.right) + 1;
        }else if(root.right==null){	//遇到左斜树
            return minDepth(root.left) + 1;
        }else{
            int left = minDepth(root.left);
            int right = minDepth(root.right);
            return Math.min(left,right) + 1;
        }
    }
}
```

这个方法不算好。

官方题解中推荐使用BFS或DFS。将在复习了图之后再来看看。

## 98. 验证二叉搜索树

使用中序遍历来获得升序序列，如果二叉搜索树合法，则序列是合法的升序序例，如果不合法，说明二叉搜索树不合法。

垃圾代码，献丑了。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    List<Integer> list = new ArrayList();
    public boolean isValidBST(TreeNode root) {
        inorderTraverse(root);
        Set<Integer> set = new HashSet<>(list);
        List<Integer> t = new ArrayList<>(set);
        // List<Integer> t = new ArrayList(list);
        for(int i=0;i<t.size();i++){
            for(int j=0;j<=i-1;j++){
                if(t.get(i) < t.get(j)){
                    int tmp = t.get(i);
                    t.set(i,t.get(j));
                    t.set(j,tmp);
                }
            }
        }
        if(list.equals(t)) return true;
        return false;
    }

    public void inorderTraverse(TreeNode root){
        if(root == null) return;
        inorderTraverse(root.left);
        list.add(root.val);
        inorderTraverse(root.right);
    }
}
```

第一次修改：将生成一个排好序的序列的做法改为判断是否有不是升序的元素：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    List<Integer> list = new ArrayList();
    public boolean isValidBST(TreeNode root) {
        inorderTraverse(root);
        List<Integer> t = new ArrayList<>(list);
        for(int i=0;i<t.size();i++){
            for(int j=0;j<=i-1;j++){
                if(t.get(i) <= t.get(j)){	//由 < 变为 <= 以包含元素相等情况
                    return false;		//出现一种则说明 不是升序序列
                }
            }
        }
        return true;
    }

    public void inorderTraverse(TreeNode root){
        if(root == null) return;
        inorderTraverse(root.left);
      	
        list.add(root.val);
        inorderTraverse(root.right);
    }

    
}
```

第二次修改：将是否为升序的判断放在遍历中：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    List<Integer> list = new ArrayList();
    boolean result = true;
    public boolean isValidBST(TreeNode root) {
        inorderTraverse(root);
        return result;
    }

    public void inorderTraverse(TreeNode root){
        if(root == null) return;
        inorderTraverse(root.left);
        if(list.size() > 0 ){
            if(root.val <= list.get(list.size()-1)){
                result = false;
            }
        }
        list.add(root.val);
        inorderTraverse(root.right);
    }
}
```

遗憾就是就算知道了result == false，程序也无法结束。可以考虑使用非递归形式的中序遍历来做。





官方题解：

📖 文字题解
方法一: 递归

思路和算法

要解决这道题首先我们要了解二叉搜索树有什么性质可以给我们利用，由题目给出的信息我们可以知道：如果该二叉树的左子树不为空，则左子树上所有节点的值均小于它的根节点的值； 若它的右子树不空，则右子树上所有节点的值均大于它的根节点的值；它的左右子树也为二叉搜索树。

这启示我们设计一个递归函数 `helper(root, lower, upper)` 来递归判断，函数表示考虑以 root 为根的子树，判断子树中所有节点的值是否都在 `(l,r)`的范围内（注意是开区间）。如果 root 节点的值 val 不在`(l,r)`的范围内说明不满足条件直接返回，否则我们要继续递归调用检查它的左右子树是否满足，如果都满足才说明这是一棵二叉搜索树。

那么根据二叉搜索树的性质，在递归调用左子树时，我们需要把上界 upper 改为 root.val，即调用 `helper(root.left, lower, root.val)`，因为左子树里所有节点的值均小于它的根节点的值。同理递归调用右子树时，我们需要把下界 lower 改为 root.val，即调用 `helper(root.right, root.val, upper)`。

函数递归调用的入口为 `helper(root, -inf, +inf)`， inf 表示一个无穷大的值。

C++:

```C++
class Solution {
public:
    bool helper(TreeNode* root, long long lower, long long upper) {
        if (root == nullptr) {
            return true;
        }
        if (root -> val <= lower || root -> val >= upper) {
            return false;
        }
        return helper(root -> left, lower, root -> val) && helper(root -> right, root -> val, upper);
    }
    bool isValidBST(TreeNode* root) {
        return helper(root, LONG_MIN, LONG_MAX);
    }
};
```



方法二：中序遍历
思路和算法

基于方法一中提及的性质，我们可以进一步知道二叉搜索树「中序遍历」得到的值构成的序列一定是升序的，这启示我们在中序遍历的时候实时检查当前节点的值是否大于前一个中序遍历到的节点的值即可。如果均大于说明这个序列是升序的，整棵树是二叉搜索树，否则不是，下面的代码我们使用栈来模拟中序遍历的过程。

可能由读者不知道中序遍历是什么，我们这里简单提及一下，中序遍历是二叉树的一种遍历方式，它先遍历左子树，再遍历根节点，最后遍历右子树。而我们二叉搜索树保证了左子树的节点的值均小于根节点的值，根节点的值均小于右子树的值，因此中序遍历以后得到的序列一定是升序序列。

![](./img/98_fig1.gif)

C++:

```C++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> stack;
        long long inorder = (long long)INT_MIN - 1;

        while (!stack.empty() || root != nullptr) {
            while (root != nullptr) {
                stack.push(root);
                root = root -> left;
            }
            root = stack.top();
            stack.pop();
            // 如果中序遍历得到的节点的值小于等于前一个 inorder，说明不是二叉搜索树
            if (root -> val <= inorder) {
                return false;
            }
            inorder = root -> val;
            root = root -> right;
        }
        return true;
    }
};
```

## 783. 二叉搜索树节点最小距离

使用了中序遍历出序列结果然后挨个相减，最后取出最小值。程序在数很大时会出错。

垃圾代码，献丑了。

```java
class Solution {
    List<Integer> list = new ArrayList();
    public int min(List<Integer> list){	//找出最小数
        int m = list.get(0);
        for(int i=0;i<list.size();i++){
            if(list.get(i) < m){
                m = list.get(i);
            }
        }
        return m;
    }
    public int minDiffInBST(TreeNode root) {
        inorderTraverse(root);
        for(int i=0;i<list.size()-1;i++){
            list.set(i,list.get(i+1) - list.get(i));
        }
        list.set(list.size()-1,10000000);	//埋下隐患
        // System.out.println(list);

        return min(list);
    }

    public void inorderTraverse(TreeNode root){
        if(root == null) return;
        inorderTraverse(root.left);
        list.add(root.val);
        inorderTraverse(root.right);
    }

}
```

官方题解：

#### 方法一：排序【通过】

**思路和算法**

将树中所有节点的值写入数组，之后将数组排序。依次计算相邻数之间的差值，找出其中最小的值。

Java：

```java
class Solution {
    List<Integer> vals;
    public int minDiffInBST(TreeNode root) {
        vals = new ArrayList();
        dfs(root);
        Collections.sort(vals);

        int ans = Integer.MAX_VALUE;
        for (int i = 0; i < vals.size() - 1; ++i)
            ans = Math.min(ans, vals.get(i+1) - vals.get(i));

        return ans;
    }

    public void dfs(TreeNode node) {
        if (node == null) return;
        vals.add(node.val);
        dfs(node.left);
        dfs(node.right);
    }
}
```

#### 方法二：中序遍历【通过】

**思路和算法**

在二叉搜索树中，中序遍历会将树中节点按数值大小顺序输出。只需要遍历计算相邻数的差值，取其中最小的就可以了。

```java
class Solution {
    Integer prev, ans;
    public int minDiffInBST(TreeNode root) {
        prev = null;
        ans = Integer.MAX_VALUE;
        dfs(root);
        return ans;
    }

    public void dfs(TreeNode node) {
        if (node == null) return;
        dfs(node.left);
        if (prev != null)
            ans = Math.min(ans, node.val - prev);
        prev = node.val;
        dfs(node.right);
    }
}
```



# 每日挑战

## 844. 比较含退格的字符串

使用了一个栈的结构来处理字符串。当遇到退格符号`'#'`时，将栈顶元素出栈。另外，如果栈空了还进行出栈的话，会出错。所以先判断是否当前栈为空。

```java
class KMStack{
    List<Character> characters = new ArrayList<>();
    public void push(Character c){
        characters.add(c);
    }
    public Character pop(){
        if(characters.isEmpty()){
            return ' ';
        }
        Character result =  characters.get(characters.size()-1);
        characters.remove(characters.size()-1);
        return result;
    }
    public String charToString(){
        StringBuilder result = new StringBuilder();
        for(Character c:characters){
            result.append(c);
        }
        return result.toString();
    }
}
class Solution {
    public boolean backspaceCompare(String S, String T) {
        
        KMStack sStack = new KMStack();
        KMStack tStack = new KMStack();
        String s = S;
        String t = T;
        char []cs = s.toCharArray();
        char []ct = t.toCharArray();
        for(char i:cs){
            if(i == '#'){
                sStack.pop();
                continue;
            }
            sStack.push(i);

        }
        for(char i:ct){
            if(i == '#'){
                tStack.pop();
                continue;
            }
            tStack.push(i);
        }
        return sStack.charToString().equals(tStack.charToString());
    }
}
```

## 925. 长键按入



我的思路是使用指针。一共使用了三个指针：

* 第一个指针nstart是用于表示 name对应的n数组中的元素
* 第二个指针t1用于表示 t2指针所指向元素前一个元素
* 第三个指针t2用于表示 typed对应的t数组中的元素

```java
class Solution {
    public boolean isLongPressedName(String name, String typed) {
        if(name.length() > typed.length()) return false;	//如果typed的长度都小于name，那一定不对
        char []n = name.toCharArray();
        char []t = typed.toCharArray();
        int nstart = 0;
        int t1 = 0;
        int t2 = 0;
        if(t1!=t.length && nstart != n.length){
            if(n[0]!=t[0]) return false;
        }
        while(t2!=t.length){
            if(nstart<n.length && n[nstart] == t[t2]){	//两个数组对应元素相等时，nstart和t2指针同时往后移动一位
                nstart++;
                t2++;
            }else if(t[t1] == t[t2]){
                t2++;
            }else{
                break;
            }
            t1 = t2-1;	//保持 t1指针指向t2前一位
        }

        return t2 == t.length && nstart == n.length;
    }
}
```

