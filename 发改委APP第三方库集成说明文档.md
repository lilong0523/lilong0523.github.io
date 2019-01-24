#发改委APP第三方库集成说明文档 

**native-echarts(Echarts图表集成)**  

####1.集成方法  
**npm install native-echarts –save**  

####2.注意  
　　(1)在集成图表功能后,可能会遇到安卓或者iOS打包后图表显示空白的情况，具体解决方法是修改*<mark>node_modules/native-echarts/src/components/Echarts/index.js</mark>*中的WebView组件：

    <WebView originWhitelist={['']} ref="chart" 
    scrollEnabled = {false}    
    injectedJavaScript = {renderChart(this.props)}    
    style={{height: this.props.height || 400,        
    backgroundColor: this.props.backgroundColor || 
    'transparent'}} scalesPageToFit={Platform.OS === 'android' ? true : false}
    source={Platform.OS==='ios' ? require('./tpl.html'):{uri:'file:///android_asset/tpl.html'}} 
    onMessage={event => this.props.onPress ?
    this.props.onPress(JSON.parse(event.nativeEvent.data)) 
    : null}/>    
    
　　(2)当图表数据没有更改，但是进行setState操作时，图表会重新绘制，解决方法如下：

    componentWillReceiveProps(nextProps) {
        if(nextProps.option !== this.props.option) { 
        	this.refs.chart.injectJavaScript(renderChart(nextProps))         
        }
    }    
   
**react-native-orientation(横竖屏切换)**  
####1.集成方法  
**npm install react-native-orientation –save**  
**react-native link react-native-orientation**

####2.注意  
iOS需要在工程中开启权限  

![avatar](https://raw.githubusercontent.com/lilong0523/pictures/master/bbq.png)

　　安卓端使用并未有任何问题，但是在iOS中，会出现用户将手机横竖切换时，界面会进行横竖屏切换的问题，需要写原生方法来配合使用，原生方法如下:
在AppDelegate.m中加入下列方法，监控手机横竖屏状态（这里监控的是横竖屏切换之前）

    - (UIInterfaceOrientationMask)application:(UIApplication *)application 
    supportedInterfaceOrientationsForWindow:(UIWindow *)window{
    	if (_allowRotate == 1) {
    		return UIInterfaceOrientationMaskLandscape;
    	}
    	else {
    			return (UIInterfaceOrientationMaskPortrait);
     	}
    }
    - (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation{
    	return YES;
    }
    - (BOOL)shouldAutorotate{
    	return YES;
    }

在AppDelegate.h中添加属性allowRotate

`@property (nonatomic,assign)NSInteger allowRotate;`

　　添加RN与原生的交互类PushCustom，在RCT_EXPORT_METHOD方法中更改allowRotate的属性，从而锁定手机横竖屏，这样就不会出现用户将手机横竖切换时的问题了。






