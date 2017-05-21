title: python 嵌入
date: 2014-11-27 16:13:10
updated: 2014-11-27 16:13:10
tags: 
- python
- 嵌入
- c++
categories:
- c++ 
---
>程序嵌入python

<!--more-->
{% codeblock lang:c python api只能互斥调用，同样自动锁实现%}
class PyThreadStateLock
{
public:
	PyThreadStateLock(void)
	{
		state = PyGILState_Ensure( );
	}

	~PyThreadStateLock(void)
	{
		PyGILState_Release( state );
	}
private:
	PyGILState_STATE state;
};
{% endcodeblock %}

{% codeblock lang:c 异常处理宏统一定义 %}
#define PCC_ERR_HANDLE											\
	PyObject *ptype, *pvalue0, *ptraceback;						\
	PyErr_Fetch(&ptype, &pvalue0, &ptraceback);					\
	PyObject*objj = PyObject_GetAttrString(pvalue0,"args");		\
	Py_DECREF(ptype);											\
	Py_DECREF(pvalue0);											\
	Py_DECREF(ptraceback);										\
	const char* errmsg;											\
	int err;													\
	int t = PyArg_ParseTuple(objj, "si", &errmsg,&err );		\
	if(!t)														\
	{															\
		Py_DECREF(objj);										\
		PyErr_Print();											\
		return -1;												\
	}															\
	NPLogError(("%s",errmsg));									\
	Py_DECREF(objj);											\
	return err;  
{% endcodeblock %}

{% codeblock lang:c 初始化定义 %}
CPyScript::CPyScript(void)
{

#if PYSUPORT
	// 初始化
	Py_NoSiteFlag = 1; //一定要加这一句
	Py_Initialize();
	// 初始化线程支持
	PyEval_InitThreads();
	// 启动子线程前执行，为了释放PyEval_InitThreads获得的全局锁，否则子线程可能无法获取到全局锁。
	PyEval_ReleaseThread(PyThreadState_Get());
#endif
}
{% endcodeblock %}
