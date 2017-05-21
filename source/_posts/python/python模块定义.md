title: python扩展模块定义
date: 2014-05-25 10:35:12
updated: 2014-05-25 10:35:27
tags: 
- python
- c++
- 扩展
categories:
- python
- cplusplus 
---
>[python c++扩展](https://github.com/niwho/pccuserppyy)

<!--more-->

{% codeblock lang:c Methodtable及入口定义%}
static PyMethodDef PCCMethods[] = {
	{"Init",  Init, METH_VARARGS, "初始化"},
	{"UnInit",  UnInit, METH_VARARGS, "卸载"},
	{"SetServeIPP",  SetServeIPP, METH_VARARGS, "建立连接"},
	{"SetJobCallback",  SetJobCallback, METH_VARARGS, "设置回调函数"},
	{"ListTrunk",  ListTrunk, METH_VARARGS, "列举trunk"},
	{"ListModules",  ListModules, METH_VARARGS, "列举模块"},
	{"ListModels",  ListModels, METH_VARARGS, "列举模型"},
	{"Execute",  Execute, METH_VARARGS, "提交作业"},
	{"QueryJobs",  QueryJobs, METH_VARARGS, "查询作业"},
	{"QueryJobsOnDoing",  QueryJobsOnDoing, METH_VARARGS, "查询作业"},
	{"QueryJobsByID",  QueryJobsByID, METH_VARARGS, "查询作业"},
	{"CancelJobs",  CancelJobs, METH_VARARGS, "取消作业"},
	{"testCallback",  testCallback, METH_VARARGS, "提交作业"},
	{"testcase1",  testcase1, METH_VARARGS, "测试用例"},
	{"testcase2",  testcase2, METH_VARARGS, "测试用例"},
	
	
	{NULL, NULL, 0, NULL}        /* Sentinel */
};

extern "C" PyMODINIT_FUNC
initPCCUserppyy(void)
{
	PyObject *m;

	PyEval_InitThreads() ;
	m = Py_InitModule("PCCUserppyy", PCCMethods);
	if (m == NULL)
		return;

	PCCError = PyErr_NewException("PCC.error", NULL, NULL);
	Py_INCREF(PCCError);
	PyModule_AddObject(m, "error", PCCError);
}
{% endcodeblock %}

{% codeblock lang:c 导出接口定义示例%}
static PyObject * ListModels(PyObject *self, PyObject *args)//static keep function name local
{
	if(gp_usr == NULL)
	{
		PyErr_SetString(PCCError, "未初始化");
		return NULL;
	}
	int sts;
	//INT64 jobkey;
	PyObject *lt;
	if (!PyArg_ParseTuple(args, "O",&lt))
		return NULL;
	if(!PyList_Check(lt))
	{
		PyErr_SetString(PCCError, "[ListModels][params] [List:check_error] ");
		return NULL;
	}

	tcps_Array<PCC_ModelPropWithKey> modelsInfo;
	sts = _ListModels(modelsInfo);

	if(TCPS_OK == sts)
	{
		for (int i=0;i<modelsInfo.Length();++i)
		{
			PyList_Append(lt,Py_BuildValue("{s:L,s:s,s:i}","id",modelsInfo[i].key
				,"name",modelsInfo[i].prop.modelTag.name.Get(),"port",modelsInfo[i].prop.ipp.port_ ));
		}
	}
	else
	{
		printf("%d,%s\n",sts,tcps_GetErrTxt((TCPSError)sts));
	}

	return PyLong_FromLong(sts);
}
{% endcodeblock %}
详见[git](https://github.com/niwho/pccuserppyy)
