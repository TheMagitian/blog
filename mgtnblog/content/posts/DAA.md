+++
title = "Dynamic Programming in C"
date = "2023-11-07T22:18:18+05:30"
author = "Srinath Anand"
authorTwitter = "" #do not include @
cover = ""
tags = ["C", "DP"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

## How to find out length of LCS - using 2 rows

```
#include<iostream>
#include<string>
using namespace std;

int lcsLength(string s1, string s2){
    int m = s1.length();
    int n = s2.length();
    int dp[2][n+1];
    int currRow = 0;
    int prevRow = 1;
    for(int i=0;i<=m;i++){
        currRow = i % 2;
        prevRow = (i+1) % 2;
        for(int j=0;j<=n;j++){
            if(i == 0 || j == 0){
                dp[currRow][j] = 0;
            }
            else if(s1[i-1] == s2[j-1]){
                dp[currRow][j] = dp[prevRow][j-1] + 1;
            }
            else{
                dp[currRow][j] = max(dp[prevRow][j], dp[currRow][j-1]);
            }
        }
    }

    return dp[currRow][n];
}

int main(){
    string s1, s2;
    cin >> s1 >> s2;
    int length = lcsLength(s1, s2);
    cout << length << endl;
    return 0;
}
```

## LCS - using 1 table only

```
#include<iostream>
#include<string>
#include<vector>
using namespace std;

int lcsLength(string s1, string s2, vector<vector<int>>& c){
    int m = s1.length();
    int n = s2.length();
    for(int i = 1; i <= m; i++){
        for(int j = 1; j <= n; j++){
            if(s1[i-1] == s2[j-1]){
                c[i][j] = c[i-1][j-1] + 1;
            }
            else{
                c[i][j] = max(c[i-1][j], c[i][j-1]);
            }
        }
    }
    return c[m][n];
}

string getLCS(string s1, string s2, const vector<vector<int>>& c){
    int i = s1.size(), j = s2.size();
    string lcs;
    while (i > 0 && j > 0) {
        if (s1[i - 1] == s2[j - 1]){
            lcs = s1[i - 1] + lcs;
            i--;
            j--;
        }
        else{
            if (c[i - 1][j] >= c[i][j - 1]){
                i--;
            }
            else{
                j--;
            }
        }
    }
    return lcs;
}

int main(){
    string s1, s2;
    cin >> s1 >> s2;
    int m = s1.length();
    int n = s2.length();
    vector<vector<int>> c(m+1, vector<int>(n+1, 0));
    int length = lcsLength(s1, s2, c);
    cout << length << endl;
    if (length > 0){
        string lcs = getLCS(s1, s2, c);
        cout << lcs << endl;
    }
    else{
        cout << "No common subsequence" << endl;
    }
    return 0;
}
```

## transform string to another using LCS

```
#include<iostream>
#include<string>
#include<vector>
using namespace std;

void printSwaps(string s1, string s2){
    vector<int> positions;
    for (int i = 0; i < s1.size(); ++i){
        if (s1[i] != s2[i]){
            int pos = s2.find(s1[i], i + 1);
            if (pos != string::npos && s1[pos] == s2[i]){
                positions.push_back(i + 1);
                positions.push_back(pos + 1);
                s2[pos] = s2[i]; 
            }
        }
    }
    if (positions.empty()){
        cout << "Not possible" << endl;
    } 
    else{
        for (int i = 0; i < positions.size(); i += 2){
            cout << positions[i] << " " << positions[i + 1] << endl;
        }
    }
}

int main(){
    string s1, s2;
    cin >> s1 >> s2;
    printSwaps(s1, s2);
    return 0;
}
```

# LCS - top-down approach

```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

int lcsLength(string &s1, string &s2, int m, int n, vector<vector<int>>& dp){
    if (m == 0 || n == 0){
        return 0;
    }
    if (dp[m][n] != -1){
        return dp[m][n];
    }
    if (s1[m - 1] == s2[n - 1]){
        dp[m][n] = 1 + lcsLength(s1, s2, m - 1, n - 1, dp);
    }
    else{
        dp[m][n] = max(lcsLength(s1, s2, m - 1, n, dp), lcsLength(s1, s2, m, n - 1, dp));
    }
    return dp[m][n];
}

string getLCS(string &s1, string &s2, vector<vector<int>> &dp){
    int m = s1.size(), n = s2.size();
    string lcs;
    while (m > 0 && n > 0){
        if (s1[m - 1] == s2[n - 1]){
            lcs += s1[m - 1];
            m--; n--;
        }
        else{
            if (dp[m][n - 1] == dp[m - 1][n]){
                m--;
            }
            else if (dp[m - 1][n] > dp[m][n - 1]){
                m--;
            }
            else{
                n--;
            }
        }
    }
    reverse(lcs.begin(), lcs.end());
    return lcs;
}

int main(){
    string s1, s2;
    cin >> s1 >> s2;
    int m = s1.length();
    int n = s2.length();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, -1));
    int length = lcsLength(s1, s2, m, n, dp);
    cout << length << '\n';
    if (length > 0){ 
        string lcs = getLCS(s1, s2, dp);
        cout << lcs << '\n';
    }
    else{ 
        cout << "No common subsequence" << '\n';
    }
    return 0;
}
```