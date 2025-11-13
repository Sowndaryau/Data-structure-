# Data-structure-
This program contains the combination of Constituency graph, voter queue, undo votes via stack, candidate tree, voter list linked, and hash table for unique IDs.  "Smart Voting Analysis Tool"

#include <iostream>
#include <unordered_map>
#include <queue>
#include <stack>
#include <vector>
#include <list>
using namespace std;

struct Candidate {
    string name;
    int votes;
    Candidate *left, *right;
    Candidate(string n) {
        name = n;
        votes = 0;
        left = right = NULL;
    }
};

Candidate* insertCandidate(Candidate* root, string name) {
    if (!root) return new Candidate(name);
    if (name < root->name)
        root->left = insertCandidate(root->left, name);
    else
        root->right = insertCandidate(root->right, name);
    return root;
}

Candidate* addVote(Candidate* root, string name) {
    if (!root) return NULL;
    if (root->name == name) root->votes++;
    else if (name < root->name) addVote(root->left, name);
    else addVote(root->right, name);
    return root;
}

void displayCandidates(Candidate* root) {
    if (!root) return;
    displayCandidates(root->left);
    cout << root->name << " - Votes: " << root->votes << endl;
    displayCandidates(root->right);
}

class Graph {
    unordered_map<string, list<string>> adj;
public:
    void addEdge(string u, string v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    void display() {
        for (auto &p : adj) {
            cout << p.first << " -> ";
            for (auto &nbr : p.second)
                cout << nbr << " ";
            cout << endl;
        }
    }
};

int main() {
    Graph g;
    g.addEdge("Constituency A", "Constituency B");
    g.addEdge("Constituency B", "Constituency C");
    g.display();
    list<string> voterList = {"Alice", "Bob", "Charlie", "David"};
    for (auto &v : voterList) cout << v << endl;
    unordered_map<string, int> voterIDs;
    int id = 101;
    for (auto &v : voterList)
        voterIDs[v] = id++;
    for (auto &p : voterIDs)
        cout << p.first << " : " << p.second << endl;
    queue<string> voterQueue;
    for (auto &v : voterList)
        voterQueue.push(v);
    queue<string> temp = voterQueue;
    while (!temp.empty()) {
        cout << temp.front() << " ";
        temp.pop();
    }
    cout << endl;
    Candidate* root = NULL;
    root = insertCandidate(root, "John");
    root = insertCandidate(root, "Emma");
    root = insertCandidate(root, "Mike");
    stack<pair<string, string>> voteStack;
    string voter, candidate;
    while (!voterQueue.empty()) {
        voter = voterQueue.front();
        voterQueue.pop();
        candidate = (voter == "Alice" || voter == "David") ? "John" : "Emma";
        addVote(root, candidate);
        voteStack.push({voter, candidate});
        cout << voter << " voted for " << candidate << endl;
    }
    if (!voteStack.empty()) {
        auto lastVote = voteStack.top();
        voteStack.pop();
        cout << "Undo vote: " << lastVote.first << " for " << lastVote.second << endl;
    }
    displayCandidates(root);
    return 0;
}
