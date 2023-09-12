# 1462-课程表IV

[1462-课程表IV](https://leetcode.cn/problems/course-schedule-iv/?envType=daily-question&envId=2023-09-12)，难度中等

![image-20230912110345597](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309121103656.png)

题解：

**深度优先+拓扑排序**

图的深度优先遍历是用 **当前节点的邻居节点进行深度的遍历** ，在深度遍历的过程中需要查看当前的节点是否已经被遍历过，如果遍历过，就不必重复遍历过程。还有一点需要注意的是：遍历到当前节点时，需要判断当前节点到其他的节点是否具有可达性。

**深度优先+拓扑排序常见于任务的安排表中。记住：拓扑排序使用的是后续遍历，至于为什么你是知道的。**

```java
class Solution {
    boolean[] visited;
    boolean[][] isPre;
	public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        List<Integer>[] graph = new ArrayList[numCourses];
        for(int i=0; i<numCourses; i++){
            graph[i] = new ArrayList<>();
        }
        for(int[] pre:prerequisites){
            int from = pre[0], to = pre[1];
            graph[from].add(to);
        }
        visited = new boolean[numCourses];
        isPre = new boolean[numCourses][numCourses];
        List<Boolean> res = new ArrayList<>();
        for(int i=0; i<numCourses; i++){
            dfs(graph, i);
        }
        for(int[] que: queries){
            res.add(isPre[que[0]][que[1]]);
        }
        return res;
    }
    
    public void dfs(List<Integer>[] graph, int s){
        if(graph[s].size()==0) return;
        if(visited[s]) return;
        visited[s] = true;
        for(int nei: graph[s]){
            dfs(graph, nei);// 这个地方不要忘记了
            isPre[s][nei] = true;
            for(int i=0; i<graph.length; i++){
                isPre[s][i] = isPre[s][i] | isPre[nei][i];
            }
        }
    }
}
```



**广度优先+拓扑排序**

```java
class Solution {
    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
		List<Integer>[] graph = new ArrayList[numCourses];
        int[] indegree = new int[numCourses];
        for(int i=0; i<numCourses; i++){
            graph[i] = new ArrayList<>();
        }
        for(int[] pre: prerequisites){
            int from = pre[0], to = pre[1];
            graph[from].add(to);
            ++indegree[to];
        }
        Queue<Integer> q = new ArrayDeque<>();
        for(int i=0; i<numCourses; i++){
            if(indegree[i]==0){
                q.offer(i);
            }
        }
        boolean[][] isPre = new boolean[numCourses][numCourses];
        List<Boolean> res = new ArrayList<>();
        while(!q.isEmpty()){
            int cur = q.poll();
            for(int nei: graph[cur]){
                isPre[cur][nei] = true;
                for(int i=0; i<numCourses; i++){
                    isPre[i][nei] = isPre[i][nei] | isPre[i][cur];
                }
                --indegree[nei];
                if(indegree[nei]==0){
                    q.offer(nei);
                }
            }
        }
        
        for(int[] qu: queries){
            res.add(isPre[qu[0]][qu[1]]);
        }
        return res;
    }
}

```