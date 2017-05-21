title: 一个简单的RESTful api
date: 2014-11-27 16:13:10
updated: 2014-11-27 16:13:10
tags: 
- python
- web
- gevent
- bottle
categories:
- python 
---
>[gevent+bottle实现](https://github.com/niwho/pcc_rest)

<!--more-->
{% codeblock lang:python 修饰函数methodroute定义规则，routeapp根据函数名生成route%}
def methodroute(route,method='GET'):
    def decorator(f):
        f.route = route
        f.method = method
        return f
    return decorator

def routeapp(obj):
    for kw in dir(obj):
        #print kw,type(kw)
        attr = getattr(obj, kw)
        if hasattr(attr, 'route'):
            #print 'aaaa:',kw,attr.route
            ll = kw.split('_')
            ll.insert(0,'/pcc')
            path = '/'.join(ll)
            print path,kw,attr.route,'@',attr.route or path,attr.method 
            bottle.route(attr.route or path,method= attr.method or 'GET')(attr)#)
{% endcodeblock %}

 
{% codeblock lang:python 异常及通用处理包装（使用修饰）：授权验证及异常处理 %}
class PCCException(PCCUserppyy.error):
    def __init__(self,errcode,errmsg):
        self.err=[]
        self.err.append(errcode)
        self.err.append(errmsg)
    def __getitem__(self,idx):
        return self.err[idx]
    def __str__(self):
        return repr(self.err)

def authed(met):
    def wrapper(*args):
        output={}
        try:
            obj = args[0]
            assert isinstance(obj,PCC)
            rt,des = obj.auth.validate(request.query.token)
            if rt>-1:
                return met(*args) #这里格式自定
            output['request']=request.path
            output['error_code']=rt
            output['error']=des
            return json.dumps(output,encoding='gbk',ensure_ascii=False) # 错误
        except PCCUserppyy.error,e:
            output['request']=request.path
            output['error_code']=e[0]
            output['error']=e[1]
            return json.dumps(output,encoding='gbk',ensure_ascii=False) # 错误
        except Exception,e:
            print traceback.format_exc()
            print e
            output['request']=request.path
            output['error_code']=-1
            #print '$$$$',e
            output['error']=str(e)
            return json.dumps(output,encoding='gbk',ensure_ascii=False) # 错误
    return wrapper
{% endcodeblock %}

{% codeblock lang:python 业务类定义 %}
class PCC(object):
    def __init__(self, config={}):
        self.config = config
        routeapp(self)
        
        self.auth = auth.Auth()
        
        LOG_FILE = '%s.log'%(time.strftime('%Y-%m-%d %A %H-%M-%S',time.localtime(time.time())),)#'tst.log'  
  
        handler = logging.handlers.RotatingFileHandler(LOG_FILE, maxBytes = 1024*1024) # 实例化handler   
        fmt = '%(asctime)s - %(filename)s:%(lineno)s - %(name)s - %(message)s'  
  
        formatter = logging.Formatter(fmt)   # 实例化formatter  
        handler.setFormatter(formatter)      # 为handler添加formatter  
  
        self.logger = logging.getLogger('pcc')    # 获取名为tst的logger  
        self.logger.addHandler(handler)           # 为logger添加handler  
        self.logger.setLevel(logging.DEBUG)  
        
        self.charsetmatch = re.compile(r'charset=([\w-]+)')
    def run(self):
        PCCUserppyy.Init()
        cf = ConfigParser.SafeConfigParser()
        cf.read(r'./conf.ini')
        ipp=cf.get('PCC','IPP')
        try:
            PCCUserppyy.SetServeIPP(ipp)
        except:
            pass
        port=8080
        host='0.0.0.0'
        try:
            port=cf.getint('LISTEN','PORT')
            host=cf.get('LISTEN','IP')
        except:
            pass
        bottle.run(server='gevent',host=host,port=port)
        #bottle.run(server='flup',host=host,port=port)
    
        
    @methodroute('/')
    @authed
    def index(self):
        print request.headers('path')
        return 'desiged by niwho&violin'
    
    @methodroute('/pcc')
    @authed
    def pcc(self):
        return 'pcc'

    @methodroute('')
    @authed
    def connect(self):
        ipp = request.query.ipp
        PCCUserppyy.SetServeIPP(ipp)
        return json.dumps({'ipp':ipp})
    #以下略.... 
{% endcodeblock %}
详见[git](https://github.com/niwho/pcc_rest)
