https://leetcode.com/problems/n-queens/


```java
class Solution {

    public boolean canPutQueen(int n,char[][] board, int ii,int jj){
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                if(board[i][j] == 'Q' && 
                    (Math.abs(i-ii) == Math.abs(j-jj) || i == ii || j == jj)) 
                    return false;
            }
        }
        return true;
    }

    public List<String> createAns(int n, char[][] board){
        List<String> ans = new ArrayList<>();
        for(int i=0;i<n;i++){
            String tmp="";
            for(int j=0;j<n;j++){
                tmp+=board[i][j];
            }
            ans.add(tmp);
        }
        return ans;
    }

    public void dfs(int n, char[][] board, List<List<String>> ans, int i, int j, int queensLeft){
        if(queensLeft == 0){ // Success scenario
            ans.add(createAns(n,board));
            return;
        }
        if(i==n || j==n) return;
        // Check if we can put a queen here..
        if(canPutQueen(n,board, i,j)) {
            board[i][j] = 'Q';
            if(j==n-1){
                dfs(n,board,ans,i+1,0,queensLeft-1);
            } else {
                dfs(n,board,ans,i,j+1,queensLeft-1);
            }
            board[i][j] = '.';
        }

        // Try without puting a queen...
        if(j==n-1){
            dfs(n,board,ans,i+1,0,queensLeft);
        } else {
            dfs(n,board,ans,i,j+1,queensLeft);
        }
    }

    public List<List<String>> solveNQueens(int n) {
        char[][] board = new char[n][n];
        for(int i=0;i<n;i++) {
            Arrays.fill(board[i],'.');
        }
        List<List<String>> ans = new ArrayList<>();
        dfs(n,board,ans,0,0,n);
        return ans;
    }
}
```