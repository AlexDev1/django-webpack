# django-webpack

## Django

Install homebrew

    which brew # check if homebrew already exists
    https://brew.sh/ # otherwise install homebrew
    https://docs.brew.sh/FAQ.html # or update brew if need be

Install python

    which python # check if built-in system python installed, e.g. /usr/bin/python
    brew install python # then install python locally
    which python3 # should return /usr/local/bin/python3

Install virtualenv

    which virtualenv # check if virtualenv already exists
    pip3 install virtualenv # otherwise install virtualenv
    https://pip.pypa.io/en/stable/user_guide/#only-if-needed-recursive-upgrade # or update virtualenv if need be

Create a virtual environment

    virtualenv venv

Activate virtual environment

    source venv/bin/activate

Install Django

    pip install django

Start project

    django-admin startproject foo

Create index.html

    mkdir foo/foo/templates
    touch foo/foo/templates/index.html

Edit index.html

    {% load static %}

    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Ba Weep Grana Weep Nini Bom</title>
            <link rel="stylesheet" href="{% static 'css/main.bundle.css' %}">
        </head>
        <body>
            <h1>
                {{ mensaje }}
            </h1>

            <div>
                <img src="{% static 'img/heart.svg' %}" alt="">
            </div>

            <div id="bar"></div>

            <script src="{% static 'js/foo.bundle.js' %}"></script>
            <script src="{% static 'js/bar.bundle.js' %}"></script>
        </body>
    </html>

Create views.py

    touch foo/foo/views.py

Edit views.py

    from django.shortcuts import render

    def index(request):
        context = {
            'mensaje': 'Saludos!',
        }
        return render(request, 'index.html', context)

Edit foo/foo/urls.py

    from django.conf.urls import url
    # from django.contrib import admin
    from . import views

    urlpatterns = [
        # url(r'^admin/', admin.site.urls),
        url(r'^$', views.index, name='index'),
    ]

Create static directory

    mkdir foo/foo/static

Create heart.svg

    mkdir foo/foo/static/img
    touch foo/foo/static/img/heart.svg

Edit heart.svg

    <svg width="32" height="32" viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><title>Heart</title><path d="M23.6 2c-3.363 0-6.258 2.736-7.599 5.594-1.342-2.858-4.237-5.594-7.601-5.594-4.637 0-8.4 3.764-8.4 8.401 0 9.433 9.516 11.906 16.001 21.232 6.13-9.268 15.999-12.1 15.999-21.232 0-4.637-3.763-8.401-8.4-8.401z"></path></svg>

Edit foo/foo/settings.py

    INSTALLED_APPS = [
        # 'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'foo',
    ]

Run server

    python foo/manage.py runserver

## Webpack

Install latest npm

    # download if not already installed, https://www.npmjs.com/get-npm#install-npm
    # otherwise...
    npm install npm@latest -g

Initialize npm

    # if npm updated from previous step,
    # then open a new tab before running this command
    npm init --yes

Install path and webpack

    npm install path webpack --save-dev

Create webpack.config.js

    touch webpack.config.js

Edit webpack.config.js

    const path = require('path');
    const ExtractTextPlugin = require('extract-text-webpack-plugin');

    module.exports = {
        entry: {
            foo: './foo/foo/assets/js/foo.js',
            bar: './foo/foo/assets/js/bar.js',
            main: './foo/foo/assets/sass/main.scss'
        },

        output: {
            filename: './js/[name].bundle.js',
            path: path.resolve('./foo/foo/static/')
        },

        module: {
            loaders: [
                {
                    test: /\.(js|jsx)$/,
                    loader: 'babel-loader',
                    exclude: /node_modules/
                },
                {
                    test: /\.scss$/,
                    loader: ExtractTextPlugin.extract(['css-loader', 'sass-loader'])
                }
            ]
        },

        plugins: [
            new ExtractTextPlugin('./css/[name].bundle.css')
        ]
    }

Create assets directory

    mkdir foo/foo/assets

Install sass

    npm install css-loader style-loader extract-text-webpack-plugin sass-loader node-sass --save-dev

Create main.scss

    mkdir foo/foo/assets/sass
    touch foo/foo/assets/sass/main.scss
    
Edit main.scss

    $body-background-color: aqua;

    @import 'reset';

    body {
        background-color: $body-background-color;
    }

Create _reset.scss

    touch foo/foo/assets/sass/_reset.scss

Edit _reset.scss

    * {
        padding: 0;
        margin: 0;
    }

Install babel

    npm install babel-loader babel-core babel-preset-es2015 babel-preset-react --save-dev

Create .babelrc

    touch .babelrc

Edit .babelrc

    {
        "presets":[
            "es2015", "react"
        ]
    }

Create foo.js

    mkdir foo/foo/assets/js
    touch foo/foo/assets/js/foo.js

Edit foo.js

    console.log('foo');

Create bar.js

    touch foo/foo/assets/js/bar.js

Edit bar.js

    import React from 'react';
    import ReactDOM from 'react-dom';
    import Figure from './components/Figure.jsx';

    // require('../sass/bar.scss');

    ReactDOM.render(<Figure />, document.getElementById('bar'));

Install react

    npm install react react-dom --save-dev

Create Heart.jsx

    mkdir foo/foo/assets/js/icons 
    touch foo/foo/assets/js/icons/Heart.jsx

Edit Heart.jsx

    import React from 'react';

    class Heart extends React.Component {
        render() {
            return (
                <svg width={this.props.width} height={this.props.height} viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg">
                    <title>{this.props.title}</title>
                    <path d="M23.6 2c-3.363 0-6.258 2.736-7.599 5.594-1.342-2.858-4.237-5.594-7.601-5.594-4.637 0-8.4 3.764-8.4 8.401 0 9.433 9.516 11.906 16.001 21.232 6.13-9.268 15.999-12.1 15.999-21.232 0-4.637-3.763-8.401-8.4-8.401z"></path>
                </svg>
            );
        }
    }

    Heart.defaultProps = {
        width: '32',
        height: '32',
        title: 'Heart',
    };

    export default Heart;

Create Figure.jsx

    mkdir foo/foo/assets/js/components 
    touch foo/foo/assets/js/components/Figure.jsx

Edit Figure.jsx

    import React from 'react';
    import Heart from '../icons/Heart.jsx';

    export default class Figure extends React.Component {
        render() {
            return (
                <figure style={{fill: 'orange'}}>
                    <Heart width="64" height="64" />
                    <img src="/static/img/heart.svg" alt="" />
                    <figcaption>Render svg inline, or reference it remotely.</figcaption>
                </figure>
            );
        }
    }

Compile or watch

    ./node_modules/.bin/webpack --config webpack.config.js
    ./node_modules/.bin/webpack --config webpack.config.js --watch

Edit package.json

    "scripts": {
      "watch": "webpack --config webpack.config.js --watch"
    },

Watch

    npm watch

## Static Files Storage

Create storage.py

    touch foo/storage.py

Edit storage.py

    from django.contrib.staticfiles.storage import HashedFilesMixin, ManifestStaticFilesStorage, StaticFilesStorage
    from django.conf import settings


    class FooStaticFilesStorage(ManifestStaticFilesStorage, StaticFilesStorage):

        patterns = HashedFilesMixin.patterns + (
            ("*.bundle.js", (
                (r"""(src:\s["']\s*(.*?)["'])""", """src: \'%s\'"""), # e.g. src: '/static/img/heart.svg'
            )),
        )

Edit foo/foo/settings.py

    # ALLOWED_HOSTS = ['localhost', '127.0.0.1', '[::1]']

    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'media/')
    STATICFILES_STORAGE = 'foo.storage.FooStaticFilesStorage'
