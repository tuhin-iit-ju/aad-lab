# aad-lab#include <iostream>
#include <vector>
#include <chrono>
#include <algorithm>
using namespace std;
using namespace std::chrono;

struct Order {
    int order_ID;
    int quantity;
};

// Sequential Search
int sequentialSearch(vector<Order>& orders, int targetID) {
    for (int i = 0; i < orders.size(); i++) {
        if (orders[i].order_ID == targetID)
            return i;
    }
    return -1;
}

// Binary Search (requires sorted order_IDs)
int binarySearch(vector<Order>& orders, int targetID) {
    int left = 0, right = orders.size() - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (orders[mid].order_ID == targetID)
            return mid;
        else if (orders[mid].order_ID < targetID)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return -1;
}

int main() {
    vector<Order> orders = {
        {101, 2}, {102, 5}, {103, 1}, {104, 4}, {105, 6},
        {106, 3}, {107, 7}, {108, 1}, {109, 2}, {110, 4},
        {111, 3}, {112, 2}, {113, 5}, {114, 1}, {115, 6},
        {116, 2}, {117, 4}, {118, 1}, {119, 7}, {120, 3},
        {121, 2}, {122, 5}, {123, 6}, {124, 3}, {125, 4}
    };

    cout << "Customers eligible for 50% discount:\n";
    for (auto& o : orders) {
        if (o.quantity > 3)
            cout << "Order_ID: " << o.order_ID << " | Quantity: " << o.quantity << endl;
    }

    // Take an ID to search
    int targetID;
    cout << "\nEnter Order ID to search for discount eligibility: ";
    cin >> targetID;

    // Sequential Search Time
    auto start1 = high_resolution_clock::now();
    int pos1 = sequentialSearch(orders, targetID);
    auto end1 = high_resolution_clock::now();
    auto duration1 = duration_cast<nanoseconds>(end1 - start1);

    // Sort before Binary Search
    sort(orders.begin(), orders.end(), [](Order a, Order b) {
        return a.order_ID < b.order_ID;
    });

    // Binary Search Time
    auto start2 = high_resolution_clock::now();
    int pos2 = binarySearch(orders, targetID);
    auto end2 = high_resolution_clock::now();
    auto duration2 = duration_cast<nanoseconds>(end2 - start2);

    cout << "\n--- Search Results ---\n";
    if (pos1 != -1)
        cout << "Sequential Search → Found Order_ID " << targetID
             << " | Quantity: " << orders[pos1].quantity << endl;
    else
        cout << "Sequential Search → Order not found.\n";

    if (pos2 != -1)
        cout << "Binary Search → Found Order_ID " << targetID
             << " | Quantity: " << orders[pos2].quantity << endl;
    else
        cout << "Binary Search → Order not found.\n";

    cout << "\nExecution Time Comparison:\n";
    cout << "Sequential Search: " << duration1.count() << " ns\n";
    cout << "Binary Search: " << duration2.count() << " ns\n";

    if (duration1 > duration2)
        cout << "\nBinary Search is faster for sorted data.\n";
    else
        cout << "\nSequential Search performed faster (may vary by data size).\n";

    return 0;
}
