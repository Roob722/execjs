## 解决pyinstaller 打包含有PyExecJS的项目运行有CMD弹框问题！
### 在_external_runtime.py的文件里有这样一句代码
```python   
def _exec_with_pipe(self, source):
            cmd = self._runtime._binary()
            p = None
            try:
                p = subprocess.Popen(cmd,stdin=PIPE, stdout=PIPE, stderr=PIPE, cwd=self._cwd, universal_newlines=True)
                input = self._compile(source)
                if six.PY2:
                    input = input.encode(sys.getfilesystemencoding())
                stdoutdata, stderrdata = p.communicate(input=input)
                ret = p.wait()
            finally:
                del p
-需要添加两句代码 


def _exec_with_pipe(self, source):
            cmd = self._runtime._binary()
            p = None
            try:
                startupinfo = subprocess.STARTUPINFO()  #
                startupinfo.dwFlags |= subprocess.STARTF_USESHOWWINDOW #
                p = subprocess.Popen(cmd, startupinfo=startupinfo,stdin=PIPE, stdout=PIPE, stderr=PIPE, cwd=self._cwd, universal_newlines=True) #这里也需要改
                input = self._compile(source)
                if six.PY2:
                    input = input.encode(sys.getfilesystemencoding())
                stdoutdata, stderrdata = p.communicate(input=input)
                ret = p.wait()
            finally:
                del p
####改完再重新打包就不会有cmd黑框了                
