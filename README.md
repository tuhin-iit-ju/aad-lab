#include <bits/stdc++.h>
using namespace std;

void dijkstra(int src, int n, vector<vector<pair<int, int>>> &adj) {
    vector<int> dist(n, INT_MAX);
    vector<int> parent(n, -1);
    dist[src] = 0;

    // Min-heap (distance, node)
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
    pq.push({0, src});

    while (!pq.empty()) {
        int u = pq.top().second;
        int d = pq.top().first;
        pq.pop();

        if (d > dist[u]) continue; // Skip if already found shorter

        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                parent[v] = u;
                pq.push({dist[v], v});
            }
        }
    }

    // Print results
    int dest = 4; // E (0=A,1=B,2=C,3=D,4=E)
    if (dist[dest] == INT_MAX) {
        cout << "No path exists from A to E\n";
        return;
    }

    cout << "Minimum cost from A to E: " << dist[dest] << " Lacs\n";

    // Reconstruct path
    vector<int> path;
    for (int v = dest; v != -1; v = parent[v])
        path.push_back(v);
    reverse(path.begin(), path.end());

    cout << "Shortest path: ";
    for (int i = 0; i < path.size(); ++i) {
        cout << char('A' + path[i]);
        if (i != path.size() - 1) cout << " -> ";
    }
    cout << endl;
}

int main() {
    int n = 5; // Cities A-E
    vector<vector<pair<int, int>>> adj(n);

    // Connections (undirected)
    adj[0].push_back({1, 10}); // A-B
    adj[1].push_back({0, 10});

    adj[0].push_back({2, 40}); // A-C
    adj[2].push_back({0, 40});

    adj[1].push_back({2, 25}); // B-C
    adj[2].push_back({1, 25});

    adj[1].push_back({3, 50}); // B-D
    adj[3].push_back({1, 50});

    adj[2].push_back({3, 15}); // C-D
    adj[3].push_back({2, 15});

    adj[2].push_back({4, 30}); // C-E
    adj[4].push_back({2, 30});

    adj[3].push_back({4, 20}); // D-E
    adj[4].push_back({3, 20});

    dijkstra(0, n, adj); // From A (index 0)
    return 0;
}
