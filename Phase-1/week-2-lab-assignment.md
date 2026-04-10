```cpp
struct CSRGraph {
    int num_vertices;
    std::vector<int> offsets;
    std::vector<int> edges;
};

CSRGraph LoadGraph(const char *filename) {
    std::ifstream file(filename);
    if (!file.is_open()) {
        std::cerr << "Error opening file\n";
        exit(1);
    }

    std::vector<std::pair<int,int>> edge_list;
    std::string line;
    int max_node = -1;

    // read file
    while (std::getline(file, line)) {
        if (line.empty() || line[0] == '#') continue;

        std::istringstream iss(line);
        int src, dst;
        iss >> src >> dst;

        edge_list.push_back({src, dst});
        max_node = std::max(max_node, std::max(src, dst));
    }

    file.close();

    int n = max_node + 1;

    CSRGraph g;
    g.num_vertices = n;
    g.offsets.resize(n + 1, 0);

    // count edges
    for (auto &e : edge_list) {
        g.offsets[e.first + 1]++;
    }

    // prefix sum
    for (int i = 1; i <= n; i++) {
        g.offsets[i] += g.offsets[i - 1];
    }

    g.edges.resize(edge_list.size());
    std::vector<int> temp = g.offsets;

    // fill edges
    for (auto &e : edge_list) {
        int src = e.first;
        int dst = e.second;

        int index = temp[src]++;
        g.edges[index] = dst;
    }

    return g;
}

Implement the `LoadGraph` function to read the graph from the provided edge list file ([soc-Slashdot0902.txt](https://drive.google.com/drive/folders/1Cr4QkLBpWa3Gp0u-9YWNE9voH9Dz4INg)) and construct the CSR representation.

Please submit your code (copy/paste your implementation) and execution result on canvas (an image to show the execution result, e.g., given a CSRGraph, print the neighbors of vertex 6).
