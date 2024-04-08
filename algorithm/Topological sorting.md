### 위상 정렬(Topological sorting)
쉬운 예로 대학의 선수 강의를 생각할 수 있다. 알고리즘 1을 들어야 알고리즘 2를 들을 수 있는 경우가 좋은 예시이다.
강의를 정점, 선후 관계를 간선으로 나타낸 그래프를 생각해보자.

<img width="1080" height="500" alt="image" src="https://github.com/movingsummer/TIL/assets/111179843/5fc4ae18-3eb0-49f1-bd21-c615378bdc8f">

강의 E는 강의 C와 D를 들어야 수강할 수 있다. C는 B를 들어야 하고, B는 A를 들어야 한다. 이와 같은 상황에서 어떤 순서로 강의를 수강해야 모든 강의를 들을 수 있을까?
이런 상황에서 사용할 수 있는 게 위상 정렬(Topological sorting)이다. 즉, 방향 그래프에서 정점 사이의 선후 관계를 위배하지 않도록 정점을 나열하는 것이라고 정의할 수 있다.

정점과 간선의 정보가 주어졌을 때, indegree가 0인 정점을 계속해서 나열하고 지워나가는 방식으로 구현할 수 있다. indegree가 0이라는 말은 해당 정점보다 우선하는 정점이 없다는 말이다. 여러 개가 있는 경우도 존재하므로 위상 정렬의 결과는 다양하게 나타날 수 있다.
위의 그래프에서는 `[A, B, C, D, E], [A, D, B, C, E]` 등이 가능할 것이다.
indegree가 0인 정점을 나열한 후 거기에서 나가는 간선은 지워준다. 앞에 정렬해야 하는 정점이 지워졌으므로 더 이상 그 정점에 대한 선후 관계는 의미가 없어지기 때문이다. 그럼 다른 정점의 indegree가 줄어들다가 0이 될 것이다. 이것을 계속 반복하면 정렬을 완료할 수 있다.

그러나, 그래프에 사이클이 존재하는 경우에는 특정 정점의 indegree가 0이 되지 않을 수 있다.

<img width="1080" height="500" alt="image" src="https://github.com/movingsummer/TIL/assets/111179843/ac94ab7a-db24-40f8-b482-9a819b25af20">

위와 같은 그래프를 보자. 정점 B, C, D 사이클을 형성하고 있다. 처음에는 indegree가 0인 A를 지울 수 있고, B로 가는 간선도 지워진다. 그러나, 그 후에도 B, C, D의 indegree는 전부 1이므로 정렬을 더 이상 진행할 수 없다.
따라서 그래프에 사이클이 있는 경우에는 위상 정렬이 불가능하다.

실제 구현은 큐나 배열 등을 활용한다. 정점과 간선 정보를 가지고 있다고 가정할 때, indegree도 알 수 있으니 0인 정점을 전부 큐에 담고 하나씩 빼면서 해당 정점과 간선을 지워준다. 해당 정점과 연결된 정점들의 indegree를 1 감소시키고 0이 됐다면 다시 큐에 담는다.
이 때, 실제로 정점과 간선의 정보를 삭제할 필요는 없고 관념적으로 indegree만 줄여주는 방식으로 구현하는 게 시간 복잡도가 더 효율적이다. indegree가 0인지 아닌지만 확인하면 되기 때문이다.

예시 코드를 살펴보자.

```cpp
int n;
int indeg[10];
vector<int> adj[10];
vector<int> result;
queue<int> q;

void topo() {
  for (int i = 0; i < 10; i++)
    if (indeg[i] == 0) q.push(i);

  while (!q.empty()) {
    auto cur = q.front();
    q.pop();
    result.push_back(cur);
    for (auto nxt : adj[cur]) {
      indeg[nxt]--;
      if (indeg[nxt] == 0) q.push(nxt);
    }
  }

  if (result.size() == n)
    for (auto v : result) cout << v << " ";
  else
    cout << "cycle exists";
}
```

정렬을 완료한 정점에 연결된 정점의 indegree만 줄여주고, 0이 됐다면 큐에 넣는 방식이다.
정렬이 완료된 결과에 담긴 정점의 수가 모든 정점의 수와 같으면 잘 수행된 것이다. 여기선 result.size()와 n이다. 앞서 말한 것처럼 사이클이 존재할 경우에는 모든 정점이 결과에 담기지 않으므로 이를 통해 사이클이 있는지 없는지 판단할 수 있다.

시간복잡도를 생각해보자. 모든 정점은 큐에 한 번씩 들어가고 모든 간선에 대해서 indegree를 1 줄이는 연산이 수행된다. 따라서 시간복잡도는 O(V+E)이다.
