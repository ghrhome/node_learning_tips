# Webpack 3 + AngularJS1.\* + Bootstrap 4 + Mapbox-gl

直接上步骤

# 一、创建项目

1. 使用VSCode建立项目目录结构如下：

![](/assets/1781-20171007134146161-484223774.png)

文档结构

```
wabg
├── http
│   ├── app.js
│   ├── controllers
│   │   └── index.js
│   ├── directives
│   │   └── index.js
│   ├── services
│   │   └── index.js
│   ├── templates
│   │   └── index.tmpl.html
│   └── views
│       └── index.js
├── package.json
└── webpack.config.js
```

二、配置webpack和angularjs

1. 安装webpack和angularjs所需的依赖项

```
npm install webpack webpack-dev-server angular angular-ui-router jquery html-webpack-plugin ngtemplate-loader html-loader --save-dev
```

2. 创建一个简单的页面，通过这个非常简单的页面，验证webpack和angular各要素是否能够正常使用，该页面要素包括:

红色部分是点击“getAuthor”按钮后由AngularJS的Controller赋值而出现的；

蓝色部分是通过一个directive渲染的

绿色部分是通过jQuery动态添加的

![](/assets/1781-20171004220759615-382815536.png)

```
wabg
├── dist
│   ├── index.html
│   └── js
│       ├── angular.bundle.js
│       ├── angular.bundle.js.map
│       ├── app.bundle.js
│       ├── app.bundle.js.map
│       ├── vendor.bundle.js
│       └── vendor.bundle.js.map
├── http
│   ├── app.js
│   ├── controllers
│   │   ├── home
│   │   │   └── home.controller.js
│   │   └── index.js
│   ├── directives
│   │   ├── demo
│   │   │   └── demo.directive.js
│   │   └── index.js
│   ├── services
│   │   └── index.js
│   ├── templates
│   │   └── index.tmpl.html
│   └── views
│       ├── demo
│       │   └── demo.view.html
│       └── index.js
├── package.json
└── webpack.config.js
```

下面为本页面用到的所有代码

2.  /webpack.config.js  文件

```
'use strict';
var path = require('path');
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    devtool: 'evel-source-map',
    context: path.resolve(__dirname + '/http'),
    entry: {
        app: './app.js',
        vendor: ['jquery'],
        angular: ['angular', 'angular-ui-router']
    },
    output: {
        path: path.resolve(__dirname + '/dist'),
        filename: 'js/[name].bundle.js',
        publicPath: '',
    },
    devServer: {
        historyApiFallback: true,
        inline: true
    },
    module: {
        rules: [
            {
                test: /\.view.html$/,
                use: [
                    {
                        loader: 'ngtemplate-loader'
                    },
                    {
                        loader: 'html-loader'
                    }
                ],
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: __dirname + '/http/templates/index.tmpl.html',
            chunksSortMode: function (chunk1, chunk2) {
                var order = ['angular', 'vendor', 'app'];
                var order1 = order.indexOf(chunk1.names[0]);
                var order2 = order.indexOf(chunk2.names[0]);
                return order1 - order2;
            }
        }),
        new webpack.optimize.CommonsChunkPlugin({
            names: ['vendor', 'angular'],
            filename: 'js/[name].bundle.js'
        }),
        new webpack.ProvidePlugin({
            $: 'jquery',
            jQuery: 'jquery',
            'window.jQuery': 'jquery'
        })
    ]
};
```

3.  /http/app.js文件

```
'use strict';
var angular = require('angular');
var uirouter = require('angular-ui-router');

var services = require('./services');
var views = require('./views');
var directives = require('./directives');
var controllers = require('./controllers');

angular.module('wabgApp', [uirouter.default, services.default, views.default, directives.default, controllers.default]);
```

4.  /http/services/index.js文件

```
'use strict';
var angular = require('angular');

var servicesModule = angular.module('wabgApp.services', []);

exports = module.exports = {
    default: servicesModule.name
};
```

5. /http/controllers/index.js文件

```
'use strict';
var angular = require('angular');

var homeController = require('./home/home.controller');

var controllersModule = angular.module('wabgApp.controllers', [])
    .controller(homeController.name, ['$scope', homeController]);

exports = module.exports = {
    default: controllersModule.name,
    homeController: homeController.name
};
```

6.  /http/controllers/home/home.controller.js文件

```
'use strict';

function homeController($scope) {
    $scope.name = 'think8848';
    $scope.getAuthor = function (name) {
        $scope.author = name;
    };
}

exports = module.exports = homeController;
```

7.  /http/directives/index.js 文件

```
'use strict';
var angular = require('angular');

var demoDirective = require('./demo/demo.directive');

var directivesModule = angular.module('wabgApp.directives', [])
    .directive(demoDirective.name, ['views', demoDirective]);

exports = module.exports = {
    default: directivesModule.name,
    demoDirective: demoDirective.name
};
```

8.  /http/directives/demo/demo.directive.js文件

```
'use strict';

function demoDirective(views) {
    return {
        restrict: 'E',
        templateUrl: views.demo,
        link: function (scope, elem) {
            $(elem).after('<div style="color: green">The content generated by jQuery</div>');
        }
    };
}

exports = module.exports = demoDirective;
```

9.  /http/views/index.js 文件

```
'use strict';
var angular = require('angular');

function views() {
    return {
        demo: require('./demo/demo.view.html')
    };
}

exports = module.exports = views();

var viewsModule = angular.module('wabgApp.views', [])
    .constant(views.name, exports);

exports.default = viewsModule.name;

```

10.  /http/views/demo/demo.view.html文件

```
<div style="color:blue">This is a demo directive.</div>
<br/>
```

11.  /http/templates/index.tmpl.html文件

```
<!DOCTYPE html>
<html ng-app="wabgApp">

<head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
    <style>
        .ng-cloak {
            display: none;
        }
    </style>
</head>

<body>
    <div id="root" ng-controller="homeController">
        The Author is:
        <lable ng-ng-cloak class="ng-cloak" style="color:red">{{author}}</lable><br/>
        <input type="button" ng-click="getAuthor(name)" value="getAuthor" />
        <br/>
        <br/>
        <demo-directive></demo-directive>
    </div>
</body>

</html>
```

12. 修改 package.json文件中的 scripts节点如下：

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack",
    "server": "webpack-dev-server --open"
  },
```

13. 运行 npm run server命令![](/assets/1781-20171004221939396-1876388506.png)

# 三、加入Bootstrap 4.0支持

1. 当前Bootstrap的最新版本是v4.0 Beta，在使用webpack时有两点需要注意：一是默认的样式文件为 scss 样式；二是当前v4.0 Beta版中默认不提供图标了。首先依然是安装依赖项

```
npm install bootstrap@4.0.0-beta  expose-loader style-loader css-loader postcss-loader precss autoprefixer sass-loader url-loader file-loader popper.js exports-loader node-sass --save-dev
```

2. 为Bootstrap加入图标支持，图标使用FontAwesome\([官网](http://fontawesome.io/)\)

2.1 先下载Bootstrap 4.0.0 beta版\(本人虽不是前端，但是对前端框架和库的版本也无力吐槽了\)的源代码，**友情提示：经测试 npm 上下载的发布包是无法顺利集成图标的**

```
git clone https://github.com/twbs/bootstrap.git
```

2.2[下载](http://fontawesome.io/assets/font-awesome-4.7.0.zip)FontAwesome字体包\(本文下载到的版本是v4.7.0\)

2.3 将下载好的FontAwesome解压后，相关的子目录放到指定位置

font-awesome 和 scss 放到 bootstrap 源代码根目录下， fonts 放到 bootstrap 源代码的 dist 目录下![](/assets/1781-20171004225406380-845816116.png)

2.4 使用VSCode打开 bootstrap 源代码根目录，修改 /scss/bootstrap.scss  文件，在最下面添加两行代码，**注意：一定要在文件最后保留一行空行**

```
$fa-font-path: "../fonts";
@import "../font-awesome/scss/font-awesome.scss";

```

![](/assets/1781-20171004233141099-530129747.png)

  
2.5 使用 npm install 下载依赖项，然后再使用 npm run dist 命令发布

在发布的过程中，跳出来很多绿的，蓝的说明没问题，过会儿就好了

![](/assets/1781-20171004230050427-1408339537.png)

2.6 使用源代码包中的内容替换wabg项目中node\_modules/bootstrap 中的相应文件夹

![](/assets/1781-20171004230539318-51221219.png)

2.7 编辑 /wabg/node\_modules/bootstrap/scss/bootstrap.scss文件，将之前代码上级目录的 ../替换为 ~bootstrap/

 ，在 fonts目录前面加上 dist目录

![](/assets/1781-20171004233052396-2144171110.png)

至此，Bootstrap的准备工作完成，下面开始添加演示代码，项目文档结构如下：

```
wabg
├── dist
│   ├── index.html
│   └── js
│       ├── angular.bundle.js
│       ├── angular.bundle.js.map
│       ├── app.bundle.js
│       ├── app.bundle.js.map
│       ├── vendor.bundle.js
│       └── vendor.bundle.js.map
├── http
│   ├── app.js
│   ├── controllers
│   │   ├── home
│   │   │   └── home.controller.js
│   │   └── index.js
│   ├── directives
│   │   ├── demo
│   │   │   └── demo.directive.js
│   │   ├── index.js
│   │   └── menu
│   │       └── menu.directive.js
│   ├── scss
│   │   └── index.scss
│   ├── services
│   │   └── index.js
│   ├── templates
│   │   └── index.tmpl.html
│   └── views
│       ├── demo
│       │   └── demo.view.html
│       ├── index.js
│       └── menu
│           └── menu.view.html
├── package.json
└── webpack.config.js
```

3. 编辑 webpack.config.js文件

```
'use strict';
var path = require('path');
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    devtool: 'evel-source-map',
    context: path.resolve(__dirname + '/http'),
    entry: {
        app: './app.js',
        vendor: ['jquery', 'popper.js', 'bootstrap', 'bootstrap/js/dist/util.js', 'bootstrap/js/dist/dropdown.js'],
        angular: ['angular', 'angular-ui-router']
    },
    output: {
        path: path.resolve(__dirname + '/dist'),
        filename: 'js/[name].bundle.js',
        publicPath: '',
    },
    devServer: {
        historyApiFallback: true,
        inline: true
    },
    module: {
        rules: [
            {
                test: /\.view.html$/,
                use: [
                    {
                        loader: 'ngtemplate-loader'
                    },
                    {
                        loader: 'html-loader'
                    }
                ],
            },
            {
                test: /\.css$/,
                use: [{
                    loader: 'style-loader'
                }, {
                    loader: 'css-loader'
                }]
            },
            {
                test: /\.(scss)$/,
                use: [{
                    loader: 'style-loader',
                }, {
                    loader: 'css-loader',
                }, {
                    loader: 'postcss-loader',
                    options: {
                        plugins: function () {
                            return [
                                require('precss'),
                                require('autoprefixer')
                            ];
                        }
                    }
                }, {
                    loader: 'sass-loader'
                }]
            },
            {
                test: /\.(jpg|png|gif)(\?.*$|$)/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 10000,
                            name: './images/[name].[ext]'
                        }
                    }
                ]
            },
            {
                test: /\.(woff|woff2|eot|ttf|svg)(\?.*$|$)/,
                use: [{
                    loader: 'file-loader',
                    options: {
                        name: './fonts/[name].[ext]'
                    }
                }]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: __dirname + '/http/templates/index.tmpl.html',
            chunksSortMode: function (chunk1, chunk2) {
                var order = ['angular', 'vendor', 'app'];
                var order1 = order.indexOf(chunk1.names[0]);
                var order2 = order.indexOf(chunk2.names[0]);
                return order1 - order2;
            }
        }),
        new webpack.optimize.CommonsChunkPlugin({
            names: ['vendor', 'angular'],
            filename: 'js/[name].bundle.js'
        }),
        new webpack.ProvidePlugin({
            $: 'jquery',
            jQuery: 'jquery',
            'window.jQuery': 'jquery',
            Popper: ['popper.js', 'default'],
            Util: 'exports-loader?Util!bootstrap/js/dist/util',
            Dropdown: 'exports-loader?Dropdown!bootstrap/js/dist/dropdown'
        })
    ]
};
```

4. 修改/http/app.js 文件

```
'use strict';
var angular = require('angular');
var uirouter = require('angular-ui-router');

var scss = require('./scss/index.scss');

var services = require('./services');
var directives = require('./directives');
var controllers = require('./controllers');
var views = require('./views');

angular.module('wabgApp', [uirouter.default, services.default, views.default, directives.default, controllers.default]);
```

5. 编辑 /http/directives/index.js文件

```
'use strict';
var angular = require('angular');

var demoDirective = require('./demo/demo.directive');
var menuDirective = require('./menu/menu.directive');

var directivesModule = angular.module('wabgApp.directives', [])
    .directive(demoDirective.name, ['views', demoDirective])
    .directive(menuDirective.name, ['views', menuDirective]);

exports = module.exports = {
    default: directivesModule.name,
    demoDirective: demoDirective.name,
    menuDirective: menuDirective
};
```

6. 创建 /http/directives/menu/menu.directive.js文件

```
'use strict';

function menuDirective(views) {
    return {
        restrict: 'E',
        templateUrl: views.menu
    };
}

exports = module.exports = menuDirective;
```

7. 编辑 /http/views/index.js  文件

```
'use strict';
var angular = require('angular');

function views() {
    return {
        demo: require('./demo/demo.view.html'),
        menu: require('./menu/menu.view.html')
    };
}

exports = module.exports = views();

var viewsModule = angular.module('wabgApp.views', [])
    .constant(views.name, exports);

exports.default = viewsModule.name;
```

8. 创建 /http/views/menu/menu.view.html 文件

```
<nav class="navbar navbar-light bg-light">
  <a class="navbar-brand" href="#">
      <span  width="30" height="30" class="d-inline-block align-top fa fa-camera-retro fa-5x" />
      Bootstrap
    </a>
</nav>
```

9. 修改 /http/templates/index.tmpl.html文件

```
<!DOCTYPE html>
<html ng-app="wabgApp">

<head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
    <style>
        .ng-cloak {
            display: none;
        }
    </style>
</head>

<body>
    <div id="root" ng-controller="homeController">
        <menu-directive></menu-directive>
        The Author is:
        <lable ng-ng-cloak class="ng-cloak" style="color:red">{{author}}</lable><br/>
        <input type="button" ng-click="getAuthor(name)" value="getAuthor" />
        <br/>
        <br/>
        <demo-directive></demo-directive>
    </div>
</body>

</html>
```

10 创建 /http/scss/index.scss 文件

```
@import "~bootstrap/scss/bootstrap";
```

11. 运行  npm run server

 就能看到效果了![](/assets/1781-20171004234610193-106744689.png)

# 四、加入Mapbox-gl支持

1. 安装依赖项

```
npm install mapbox-gl angular-mapboxgl-directive --save-dev
```

 2. 下面演示的是AngularJS中使用MapBox-gl-js，为了显示效果，加入了一个很简单的About页面，用于演示地图和普通VIew的切换。如下图所示：

![](/assets/1781-20171005143625583-1337106235.png)

项目目录结构如下

```
wabg
├── dist
│   ├── fonts
│   │   ├── fontawesome-webfont.eot
│   │   ├── fontawesome-webfont.svg
│   │   ├── fontawesome-webfont.ttf
│   │   ├── fontawesome-webfont.woff
│   │   └── fontawesome-webfont.woff2
│   ├── index.html
│   └── js
│       ├── angular.bundle.js
│       ├── angular.bundle.js.map
│       ├── app.bundle.js
│       ├── app.bundle.js.map
│       ├── vendor.bundle.js
│       └── vendor.bundle.js.map
├── http
│   ├── app.js
│   ├── controllers
│   │   ├── about
│   │   │   └── about.controller.js
│   │   ├── home
│   │   │   └── home.controller.js
│   │   ├── index.js
│   │   └── map
│   │       └── map.controller.js
│   ├── css
│   │   └── index.css
│   ├── directives
│   │   ├── demo
│   │   │   └── demo.directive.js
│   │   ├── index.js
│   │   └── menu
│   │       └── menu.directive.js
│   ├── scss
│   │   └── index.scss
│   ├── services
│   │   ├── index.js
│   │   └── map-styles
│   │       └── default-style.js
│   ├── templates
│   │   └── index.tmpl.html
│   └── views
│       ├── about
│       │   └── about.view.html
│       ├── demo
│       │   └── demo.view.html
│       ├── index.js
│       ├── map
│       │   └── map.view.html
│       └── menu
│           └── menu.view.html
├── package.json
└── webpack.config.js

```

3. 修改 webpack.config.js文件

```
'use strict';
var path = require('path');
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    devtool: 'evel-source-map',
    context: path.resolve(__dirname + '/http'),
    entry: {
        app: './app.js',
        vendor: ['jquery', 'popper.js', 'bootstrap', 'bootstrap/js/dist/util.js', 'bootstrap/js/dist/dropdown.js', 'mapbox-gl', 'angular-mapboxgl-directive'],
        angular: ['angular', 'angular-ui-router']
    },
    output: {
        path: path.resolve(__dirname + '/dist'),
        filename: 'js/[name].bundle.js',
        publicPath: '',
    },
    devServer: {
        historyApiFallback: true,
        inline: true
    },
    module: {
        rules: [
            {
                test: /\.view.html$/,
                use: [
                    {
                        loader: 'ngtemplate-loader'
                    },
                    {
                        loader: 'html-loader'
                    }
                ],
            },
            {
                test: /\.css$/,
                use: [{
                    loader: 'style-loader'
                }, {
                    loader: 'css-loader'
                }]
            },
            {
                test: /\.(scss)$/,
                use: [{
                    loader: 'style-loader',
                }, {
                    loader: 'css-loader',
                }, {
                    loader: 'postcss-loader',
                    options: {
                        plugins: function () {
                            return [
                                require('precss'),
                                require('autoprefixer')
                            ];
                        }
                    }
                }, {
                    loader: 'sass-loader'
                }]
            },
            {
                test: /\.(jpg|png|gif)(\?.*$|$)/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 10000,
                            name: './images/[name].[ext]'
                        }
                    }
                ]
            },
            {
                test: /\.(woff|woff2|eot|ttf|svg)(\?.*$|$)/,
                use: [{
                    loader: 'file-loader',
                    options: {
                        name: './fonts/[name].[ext]'
                    }
                }]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: __dirname + '/http/templates/index.tmpl.html',
            chunksSortMode: function (chunk1, chunk2) {
                var order = ['angular', 'vendor', 'app'];
                var order1 = order.indexOf(chunk1.names[0]);
                var order2 = order.indexOf(chunk2.names[0]);
                return order1 - order2;
            }
        }),
        new webpack.optimize.CommonsChunkPlugin({
            names: ['vendor', 'angular'],
            filename: 'js/[name].bundle.js'
        }),
        new webpack.ProvidePlugin({
            $: 'jquery',
            jQuery: 'jquery',
            'window.jQuery': 'jquery',
            Popper: ['popper.js', 'default'],
            Util: 'exports-loader?Util!bootstrap/js/dist/util',
            Dropdown: 'exports-loader?Dropdown!bootstrap/js/dist/dropdown',
            mapboxgl: 'mapbox-gl'
        })
    ]
};
```

4. 修改 /http/app.js文件

```
'use strict';
var angular = require('angular');
var uirouter = require('angular-ui-router');

var css = require('./css/index.css');
var scss = require('./scss/index.scss');

var services = require('./services');
var directives = require('./directives');
var controllers = require('./controllers');
var views = require('./views');

var mapboxglDirective = 'mapboxgl-directive';

angular.module('wabgApp', [uirouter.default, services.default, views.default, directives.default, controllers.default, mapboxglDirective])
    .config(function ($stateProvider) {
        $stateProvider
            .state('home', {

            })
            .state('about', {
                url: '/about',
                controller: controllers.aboutController,
                templateUrl: views.about
            })
            .state('map', {
                url: '/map',
                controller: controllers.mapController,
                templateUrl: views.map
            });
    })
    .run([function () {
        mapboxgl.accessToken = true;
    }]);
```

5. 修改 /http/services/index.js文件

```
'use strict';
var angular = require('angular');

var defaultMapStyle = require('./map-styles/default-style');

var servicesModule = angular.module('wabgApp.services', [])
    .constant(defaultMapStyle.name, defaultMapStyle());

exports = module.exports = {
    default: servicesModule.name
};
```

6. 创建 /http/services/map-styles/default-style.js文件

```
'use strict';

function defaultMapStyle() {
    return {
        'version': 8,
        'sources': {
            'raster-tiles': {
                type: 'raster',
                'tiles': ['http://mt3.google.cn/vt/lyrs=s&hl=zh-CN&gl=cn&x={x}&y={y}&z={z}'],
                'tileSize': 256
            }
        },
        'layers': [{
            'id': 'simple-tiles',
            'type': 'raster',
            'source': 'raster-tiles',
            'minzoom': 0,
            'maxzoom': 22
        }]
    };
}

exports = module.exports = defaultMapStyle;
```

7. 修改 

/http/controllers/index.js

 文件

```
'use strict';
var angular = require('angular');

var homeController = require('./home/home.controller');
var mapController = require('./map/map.controller');
var aboutController = require('./about/about.controller');

var controllersModule = angular.module('wabgApp.controllers', [])
    .controller(homeController.name, ['$scope', homeController])
    .controller(mapController.name, ['$scope', 'defaultMapStyle', mapController])
    .controller(aboutController.name, ['$scope', aboutController]);

exports = module.exports = {
    default: controllersModule.name,
    homeController: homeController.name,
    mapController: mapController.name,
    aboutController: aboutController.name
};
```

8. 创建 /http/controllers/map/map.controller.js文件

```
'use strict';

function mapController($scope, defaultMapStyle) {
    $scope.glStyle = defaultMapStyle;
}

exports = module.exports = mapController;
```

9. 创建 /http/controllers/about/about.controller.js文件

```
'use strict';

function aboutController($scope){
    $scope.text = 'webpack 3 + angularjs 1.* + bootstrap 4 + mapbox-gl demo';
}

exports = module.exports = aboutController;
```

10. 修改 /http/views/index.js文件

```
'use strict';
var angular = require('angular');

function views() {
    return {
        demo: require('./demo/demo.view.html'),
        menu: require('./menu/menu.view.html'),
        map: require('./map/map.view.html'),
        about: require('./about/about.view.html')
    };
}

exports = module.exports = views();

var viewsModule = angular.module('wabgApp.views', [])
    .constant(views.name, exports);

exports.default = viewsModule.name;
```

11. 修改 /http/views/menu/menu.view.html文件

```
<nav class="navbar navbar-light bg-light justify-content-between">
  <a class="navbar-brand" href="" ui-sref="map">Map</a>
  <a class="navbar-brand" href="" ui-sref="about">About</a>
  <form class="form-inline">
    <input class="form-control mr-sm-2" type="text" placeholder="Search" aria-label="Search">
    <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
  </form>
</nav>
```

12. 创建 /http/views/map/map.view.html文件

```
<div>
    <div mapboxgl gl-controls="glControls" gl-style="glStyle"></div>
</div>
```

13. 创建 /http/views/about/about.view.html文件

```
<br />
<div style="font-weight:bold;font-size:18px">About: {{text}}</div>
```

14. 修改 /http/templates/index.tmpl.html文件

```
<!DOCTYPE html>
<html ng-app="wabgApp">

<head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
    <style>
        .ng-cloak {
            display: none;
        }
    </style>
</head>

<body>
    <div id="root" ng-controller="homeController">
        <menu-directive></menu-directive>
        The Author is:
        <lable ng-ng-cloak class="ng-cloak" style="color:red">{{author}}</lable><br/>
        <input type="button" ng-click="getAuthor(name)" value="getAuthor" />
        <br/>
        <br/>
        <demo-directive></demo-directive>
        <ui-view></ui-view>
    </div>
</body>

</html>
```

15. 创建 /http/css/index.css 文件

```
@import '~mapbox-gl/dist/mapbox-gl.css';
@import '~angular-mapboxgl-directive/dist/angular-mapboxgl-directive.css';
```

16. 运行 npm run server查看效果

![](/assets/1781-20171005143937302-62417588.png)

源码分享地址：

[https://gitee.com/think8848/wabg.git](https://gitee.com/think8848/wabg.git)





















