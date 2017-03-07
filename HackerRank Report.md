# AdvancedGameProgrammingCW1
# Abdullah Bin Abdullah

# Introduction
Link to HackerRank profile: https://www.hackerrank.com/Dulz01

For the Advanced Programming First Assignment, I solved problems from HackerRank in the C++ section of the site. I answered all of the questions except some in the "Other Concepts" subdomain of C++. As of the time of writing I have achieved 730 points on HackerRank. Most of the questions work from having a sample input and then making sure your program's output matches the sample output. Below are some notable questions and my solution for them.

# Virtual Functions | Points: 40 | Difficulty: Medium
The problem brief:
https://www.hackerrank.com/challenges/virtual-functions

The purpose of this question is to become familiar with the use of virtual functions. The problem asks for three classes: Person, Professor and Student and continues to explain how these classes should be built and how they interact with each other. The question asks to build these classes using virtual functions, constructors and static variables. The editor in this question locks the user from adding anymore includes and from the main() function.

Full Solution:
```
class Person {
protected:
    char name[100];
    int age;

public:
    Person(){}    
    
    virtual void getdata() = 0;
    virtual void putdata() = 0;
};

static int professor_id = 0;
static int student_id = 0;

class Professor : public Person {
    int publications, cur_id;
    
public:
    Professor() : publications(0) {
        professor_id++;
        cur_id = professor_id;
    }
    virtual void getdata() { scanf("%s %d %d", &name[0], &age, &publications); }
    virtual void putdata() { printf("%s %d %d %d\n", name, age, publications, cur_id); }
};

class Student : public Person {
    int marks[6], cur_id, sum;

public:
    Student() : sum(0) {
        student_id++;
        cur_id = student_id;
    }
    virtual void getdata() {
        scanf("%s %d", &name[0], &age);
        for (int i = 0; i < 6; i++) {
            scanf("%d", &marks[i]);
        }
    }
    virtual void putdata() {
        for (int i = 0; i < 6; i++) {
            sum += marks[i];
        }
        printf("%s %d %d %d\n", name, age, sum, cur_id);
    }
};
```
This question helped reinforce my knowledge on the use of virtual functions, static variables and building classes. It was the first question where the answer is slightly more substantial. The fact that the question only asks that you build the classes was helpful because I didn't have to worry about outputting correctly.

# Attribute Parser | Points: 35 | Difficulty: Medium
The problem brief:
https://www.hackerrank.com/challenges/attribute-parser

This question was the first problem I had which took significant time to solve. The question asks to manage their own mark up language and the attributes stored in the tags. The question asks to deal with entering tags into the program and then processing queries for those tags. The HackerRank editor allows the full source code to be editable.

The trouble I had with this question is manipulating the strings between the tags as well as putting in some sort of hierarchy. I stored the queries and HRML code into vectors before manipulating the strings they contain. I deleted the quotation marks and the closing pointy brackets at the end of the tag and handled the rest of the tag characters by storing them into a char variable and leave it unused. The rest of the tags, such as tag name, the type of attribute and the value of the attribute are stored in their own variable to be used in manipulating the strings for the queries.

The tag hierarchy is handled by appending the tagname to a string if there is an existing tag and then pushes it into a tag vector. The tag is popped out of the vector if a closing tag is found.

Full Solution:
```
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
#include <sstream>
#include <map>
using namespace std;

vector<string> get_hrml(int n) {
    string temp;
    vector<string> hrml;
    
    for(int i = 0; i < n; i++) {
        getline(cin, temp);
        hrml.push_back(temp);
    }
    return hrml;
}

vector<string> get_queries(int q) {
    string temp;
    vector<string> queries;
    
    for(int i = 0; i < q; i++) {
        getline(cin, temp);
        queries.push_back(temp);
    }
    return queries;
}

int main() {
    int n, q = 0;
    vector<string> hrml, queries, tags;
    string buffer, tag, attribute, value;
    map<string, string> tagAttribute;
    char ch;
    
    cin >> n >> q;
    cin.ignore();
    
    hrml = get_hrml(n);
    queries = get_queries(q);
    
    for(int i = 0; i < n; i++) {
        buffer = hrml[i];
        buffer.erase(remove(buffer.begin(), buffer.end(), '\"' ),buffer.end());
        buffer.erase(remove(buffer.begin(), buffer.end(), '>' ),buffer.end());
        
        if(buffer.substr(0,2)=="</"){
            tags.pop_back();
        }
        else { 
            stringstream ss;
            ss.str("");
            ss << buffer;
            ss >> ch >> tag >> attribute >> ch >> value;
            
            string temp1;   
            
            if(tags.size() > 0) {
                temp1 = *tags.rbegin();
                temp1 += "." + tag ;
            }
            else {
                temp1 = tag;
            }
            tags.push_back(temp1);
            tagAttribute[*tags.rbegin() + "~" + attribute] = value;
            
            while(ss) {
                ss >> attribute >> ch >> value;
                tagAttribute[*tags.rbegin() + "~" + attribute] = value;
            }
        }
    }
    
    for (int i = 0; i < q; i++) {
        if(tagAttribute.find(queries[i]) == tagAttribute.end()) {
            printf("Not Found!\n");
        }
        else {
            printf("%s\n", tagAttribute[queries[i]].c_str());
        }
    }
    
    return 0;
}
```
From reading the discussions for this problem, I found that I could've used regular expressions to improve this code, instead of manually manipulating strings to achieve the desired output. I learnt the intricacies of string::erase in this problem as well as using string iterators.

# Abstract Classes - Polymorphism | Points: 60 | Difficulty: Hard
The problem brief:
https://www.hackerrank.com/challenges/abstract-classes-polymorphism

This question tests your knowledge of abstract base classes. The problem asks you to create an LRUCache class that inherits from Cache. The editor doesn't allow you to edit main(), the #includes and the Cache class. This problem is more about managing a double linked list than it is about inheriting from an abstract base class and I had to move this to Visual Studio as I was trying to debug segmentation faults.

Full Solution:
```
class LRUCache : public Cache {
private:
  void move_node_to_head(Node* temp_node) {
    head->prev = temp_node;
    temp_node->next = head;
    head = temp_node;
  }

  void unlink_node(Node* temp_node) {
    temp_node->prev->next = temp_node->next;
    temp_node->next->prev = temp_node->prev;
    temp_node->prev = NULL;
    temp_node->next = NULL;
  }

  void unlink_tail_node(Node* temp_node) {
    temp_node->prev->next = NULL;
    tail = temp_node->prev;
    temp_node->prev = NULL;
  }

public:
  LRUCache(int capacity) {
    cp = capacity;
    tail = NULL;
    head = NULL;
    mp.clear();
  }

  void set(int key, int val) {
    Node* temp_node;
    
    auto it = mp.find(key);

    if (it != mp.end()) {
      temp_node = it->second;
      temp_node->value = val;
      if (temp_node == head) {
        ;
      }
      else if (temp_node == tail) {
        unlink_tail_node(temp_node);
        move_node_to_head(temp_node);
      }
      else {
        unlink_node(temp_node);
        move_node_to_head(temp_node);
      }
    }
    else {
      temp_node = new Node(key, val);

      if (mp.size() == 0) {
        mp[key] = temp_node;
        head = temp_node;
        tail = temp_node;
      }

      else if (mp.size() == 1 && cp == 1) {
        mp.clear();
        mp[key] = temp_node;
        delete head;
        head = temp_node;
        tail = temp_node;

      }
      else if (mp.size() == cp) {
        Node* new_tail;
        tail->prev->next = NULL;
        mp.erase(tail->key);
        new_tail = tail->prev;
        delete tail;
        tail = new_tail;

        mp[key] = temp_node;
        move_node_to_head(temp_node);
      }
      else {
        mp[key] = temp_node;
        move_node_to_head(temp_node);
      }
    }
  }

  int get(int key) {
    Node* temp_node;
    auto it = mp.find(key);

    if (it != mp.end()) {
      temp_node = it->second;
      if (temp_node == head) {
        ;
      }
      else if (temp_node == tail) {
        unlink_tail_node(temp_node);
        move_node_to_head(temp_node);
      }
      else {
        unlink_node(temp_node);
        move_node_to_head(temp_node);
      }
      return temp_node->value;
    }
    else {
      return -1;
    }
  }

};
```
This question helped me understand the linked list data structure as well as solidifying my knowledge of pointers. A lot of time was spent making sure the pointers are used correctly.

# Deque-STL | Points: 50 | Difficulty: Hard
The problem brief:
https://www.hackerrank.com/challenges/deque-stl

The problem asks for the maximum integer of a subarray within an array. The editor allows you to fully edit the source code. Initially, I made use of the deque by sorting the numbers by pushing smaller numbers to the front and larger numbers to the back and then printing the back of the deque but I was lost when I had to handle numbers going out of range of the subarray. This is also the first question I encountered which had time requirement when running the code. Initially I had a nested for loop handling the array and subarrays but had to separate them in order to meet the time requirements for the test cases.

Full Solution:
```
#include <iostream>
#include <algorithm>
#include <deque> 
using namespace std;

void printKMax(int arr[], int n, int k){
    //Write your code here.
    deque<int> dq;
    deque<int>::iterator it;
    int maxVal = 0;
    for (int i = 0; i < k; i++) {
        dq.push_back(arr[i]);
        if (arr[i] > maxVal) {
            maxVal = arr[i];
        }            
    }
    
    for (int i = 0; i < n-k; i++) {
        cout << maxVal << ' ';
        int out_of_scope = dq.front();
        dq.pop_front();
        dq.push_back(arr[k + i]);
        if (out_of_scope >= maxVal) {
            it = max_element(dq.begin(), dq.end());
            maxVal = *it;
        }
        else if (arr[k + i] > maxVal) {
            maxVal = arr[k + i];
        } 
    }
    cout << maxVal << endl;
    
}
int main(){
  
   int t;
   cin >> t;
   while(t>0) {
      int n,k;
       cin >> n >> k;
       int i;
       int arr[n];
       for(i=0;i<n;i++)
            cin >> arr[i];
       printKMax(arr, n, k);
       t--;
     }
     return 0;
}
```
This problem helped understand how to make my code a little more efficient moreso than understanding deques. I find that the test cases differ slightly to what the question is actually asking. This seems to be a common thing with the questions in the C++ section of HackerRank.

# Magic Spells | Points: 40 | Difficulty: Hard
The problem brief:
https://www.hackerrank.com/challenges/magic-spells

This was an interesting problem to solve, especially because of how it was worded. The question is asking for you to write a function that identifies the kind of spell that is cast against you. There are inherited classes of spells that you must check against and a generic spell that you must find the Longest Common Subsequence (LCS) for.
Using dynamic_casts I checked if the spell is any of the inherited classes and then call their function which reveals information about them. With the help of Leyla, I grasped at the very least the naive way of finding the LCS using 2D vectors. 

Full Solution:
```
Fireball* fireball = dynamic_cast<Fireball*>(spell);
if (fireball != NULL) {
    fireball->revealFirepower();
    return;
}

Frostbite* frostbite = dynamic_cast<Frostbite*>(spell);
if (frostbite != NULL) {
    frostbite->revealFrostpower();
    return;
}
Thunderstorm* thunderstorm = dynamic_cast<Thunderstorm*>(spell);
if (thunderstorm != NULL) {
    thunderstorm->revealThunderpower();
    return;
}
Waterbolt* waterbolt = dynamic_cast<Waterbolt*>(spell);
if (waterbolt != NULL) {
    waterbolt->revealWaterpower();
    return;
}

string scroll = spell->revealScrollName();
string sj = SpellJournal::journal;

int m = sj.length()+1;
int n = scroll.length()+1;

vector < vector<int> > longest(m, vector<int>(n));
for(int i = 1;i<m; i++ ){
    for(int j=1; j<n; j++){
        if(scroll[j-1]==sj[i-1]) {
            longest[i][j]= longest[i-1][j-1]+1;
        }
        else {
            if(longest[i][j-1]>longest[i-1][j]) {
                longest[i][j]= longest[i][j-1];
            }
            else {
                    longest[i][j]= longest[i-1][j];
                }
            }
        }
    }

cout<<longest[m-1][n-1]<<endl;
```
I really liked this problem because it's requirements weren't hidden away in the test cases and the question was phrased in an interesting way. 

# Conclusion
This website is really helpful in reinforcing my programming knowledge but I am wary of the poorly worded questions, hidden requirements from the test cases and sometimes code that teaches you bad practices. For example, there is one question where it asks you to use #defines to fix bad code. Notable things I've learnt from this section is the manipulation of strings, the linked list data structure and finding the longest common subsequence.