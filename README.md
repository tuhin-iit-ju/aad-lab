#include <bits/stdc++.h>
using namespace std;

/*-------------------------------------------------------
  PHASE 1: Resource Acquisition (Greedy 0/1 Knapsack)
--------------------------------------------------------*/
struct Item {
    string id;
    double cost;
    int value;
};

bool cmpRatio(Item a, Item b) {
    double r1 = (double)a.value / a.cost;
    double r2 = (double)b.value / b.cost;
    return r1 > r2; // sort by decreasing ratio
}

void phase1() {
    cout << "----- Phase 1: Greedy Resource Acquisition -----\n";
    double budget = 500000;

    vector<Item> items = {
        {"P1 (Food Rations)", 300000, 40},
        {"P2 (Water Kits)", 100000, 25},
        {"P3 (Shelters)", 350000, 50},
        {"P4 (Medical Supplies)", 100000, 18},
        {"P5 (Comm Equipment)", 50000, 12}
    };

    sort(items.begin(), items.end(), cmpRatio);

    double totalCost = 0;
    int totalValue = 0;
    vector<string> chosen;

    for (auto &it : items) {
        if (totalCost + it.cost <= budget) {
            totalCost += it.cost;
            totalValue += it.value;
            chosen.push_back(it.id);
        }
    }

    cout << "\nItems selected using Greedy (Value/Cost ratio):\n";
    for (auto &id : chosen) cout << "  " << id << "\n";
    cout << "Total Cost = " << totalCost << " BDT\n";
    cout << "Total Impact Score = " << totalValue << "\n\n";

    cout << "💡 (Note: Greedy is heuristic. Actual optimal may differ.)\n\n";
}

/*-------------------------------------------------------
  PHASE 2: Resource Distribution (Edmonds-Karp Max Flow)
--------------------------------------------------------*/
class MaxFlow {
    int n;
    vector<vector<int>> capacity;
    vector<vector<int>> adj;

public:
    MaxFlow(int nodes) {
        n = nodes;
        capacity.assign(n, vector<int>(n, 0));
        adj.assign(n, {});
    }

    void addEdge(int u, int v, int cap) {
        adj[u].push_back(v);
        adj[v].push_back(u);
        capacity[u][v] = cap;
    }

    int bfs(int s, int t, vector<int>& parent) {
        fill(parent.begin(), parent.end(), -1);
        parent[s] = -2;
        queue<pair<int, int>> q;
        q.push({s, INT_MAX});

        while (!q.empty()) {
            int u = q.front().first;
            int flow = q.front().second;
            q.pop();

            for (int v : adj[u]) {
                if (parent[v] == -1 && capacity[u][v] > 0) {
                    parent[v] = u;
                    int new_flow = min(flow, capacity[u][v]);
                    if (v == t)
                        return new_flow;
                    q.push({v, new_flow});
                }
            }
        }
        return 0;
    }

    int edmondsKarp(int s, int t) {
        int flow = 0;
        vector<int> parent(n);
        int new_flow;

        while (new_flow = bfs(s, t, parent)) {
            flow += new_flow;
            int cur = t;
            while (cur != s) {
                int prev = parent[cur];
                capacity[prev][cur] -= new_flow;
                capacity[cur][prev] += new_flow;
                cur = prev;
            }
        }
        return flow;
    }
};

void phase2() {
    cout << "----- Phase 2: Max Flow (Distribution Network) -----\n";

    // Node indexing
    map<string, int> node = {
        {"S", 0}, {"T1", 1}, {"T2", 2}, {"P1", 3},
        {"P2", 4}, {"P3", 5}, {"D", 6}, {"E", 7}, {"Sink", 8}
    };

    MaxFlow mf(9);

    // Add edges (capacities in tons/day)
    mf.addEdge(node["S"], node["T1"], 20);
    mf.addEdge(node["S"], node["T2"], 10);
    mf.addEdge(node["T1"], node["P1"], 15);
    mf.addEdge(node["T1"], node["P3"], 8);
    mf.addEdge(node["T2"], node["P2"], 7);
    mf.addEdge(node["P1"], node["P3"], 5);
    mf.addEdge(node["P1"], node["D"], 10);
    mf.addEdge(node["P2"], node["D"], 10);
    mf.addEdge(node["P3"], node["D"], 12);
    mf.addEdge(node["D"], node["E"], 25);
    mf.addEdge(node["E"], node["Sink"], 15);

    int maxFlow = mf.edmondsKarp(node["S"], node["Sink"]);

    cout << "Maximum possible flow from S to Sink = "
         << maxFlow << " tons/day\n";
}

/*-------------------------------------------------------
  MAIN PROGRAM
--------------------------------------------------------*/
int main() {
    phase1();
    phase2();
    return 0;
}
