title: map 元素删除
date: 2014-06-12 18:29:07
updated: 2014-06-12 18:29:11
tags: 
- c++ 
- program
---


    int main(int argc, char* argv[])  
    {  
        map<string, string> mapData;  
    
        mapData["a"] = "aaa";   
        mapData["b"] = "bbb";   
        mapData["c"] = "ccc";   
  
  
        for (map<string, string>::iterator i=mapData.begin(); i!=mapData.end(); /*i++*/)  
        {  
            if (i->first == "b")  
            {  
            mapData.erase(i++);  
            }  
            else  
            {  
                i++;  
            }  
        }  
        return 0;  
    }


