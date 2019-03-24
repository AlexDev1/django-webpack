# django-webpack

## Gather tools

Install brew

    # check if brew already installed, e.g. /usr/local/bin/brew
    which brew
    # if already installed, update brew
    brew update && brew upgrade && brew cleanup
    # otherwise, follow the installation instructions at https://brew.sh/

Install nginx

    # check if nginx already installed, e.g. /usr/local/bin/nginx
    which nginx
    # otherwise, install nginx
    brew install nginx

Install node

    # check if node already installed, e.g. /usr/local/bin/node
    which node
    # otherwise, install node
    brew install node

Install python

    # check if python already installed, e.g. /usr/local/bin/python3
    which python3
    # if not, install python
    brew install python

Install/upgrade virtualenv

    pip3 install --upgrade virtualenv

## Set up python environment

Create virtual environment

    virtualenv venv

Enter virtual environment

    source venv/bin/activate

Install gunicorn

    pip3 install --upgrade gunicorn

Install django

    pip3 install --upgrade django

Install django-debug-toolbar

    pip3 install --upgrade django-debug-toolbar

Start project

    # this will generate a `manage.py` file and a `ultra` directory
    django-admin startproject ultra .

Exit virtual environment

    deactivate

## Install npm packages

Initialize npm

    # this will generate a `package.json` file
    npm init --yes

Install webpack

    # this will generate a `package-lock.json` file and a `node_modules` directory
    npm install --save-dev webpack webpack-cli webpack-merge

Install loaders

    npm install --save-dev babel-loader css-loader file-loader sass-loader style-loader

Install babel

    npm install --save-dev @babel/cli @babel/core @babel/preset-env @babel/plugin-transform-react-jsx

Install preact

    npm install --save-dev preact

Install node-sass

    npm install --save-dev node-sass

Install mini-css-extract-plugin

    npm install --save-dev mini-css-extract-plugin

Install normalize.css

    npm install --save-dev normalize.css

Review packages

    npm list --depth=0

## Configure babel and webpack

Create .babelrc

    touch .babelrc

Edit .babelrc

    {
        "presets": [
            "@babel/preset-env"
        ],
        "plugins": [
            ["@babel/plugin-transform-react-jsx", { "pragma":"h" }]
        ]
    }

Create configuration files

    touch webpack.common.js
    touch webpack.dev.js
    touch webpack.prod.js

Edit webpack.common.js

    const path = require('path');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');

    module.exports = {
        entry: {
            // establish `css/` naming convention for all CSS entry points
            'css/main': './ultra/assets/css/main.scss',
            // establish `js/` naming convention for all JavaScript entry points
            'js/itsy': './ultra/assets/js/itsy.js'
        },
        output: {
            // resolve compiled files to '/static/[see-filename-function-below]'
            publicPath: '/static/',
            // save compiled files into `ultra/static` directory
            path: path.resolve(__dirname, 'ultra/static'),
            // save compiled files as...
            filename: (chunkData) => {
                // check for CSS filenames [1]
                let matched = chunkData.chunk.name.match(/^css\//);
                // dump CSS files into `_ignore` directory [2]
                return matched ? '_ignore/[name].bundle.js' : '[name].bundle.js';
            }
        },
        module: {
            rules: [
                {
                    test: /\.(js|jsx)$/,
                    exclude: /node_modules/,
                    use: {
                        loader: 'babel-loader',
                        options: {
                            presets: ['@babel/preset-env']
                        }
                    }
                },
                {
                    test: /\.scss$/,
                    use: [
                        MiniCssExtractPlugin.loader,
                        'css-loader',
                        'sass-loader'
                    ]
                },
                {
                    test: /\.(jpg|png|svg)$/,
                    use: [
                        {
                            loader: 'file-loader',
                            options: {
                                name: 'img/[name].[ext]'
                            },
                        },
                    ]
                }
            ]
        },
        plugins: [
            new MiniCssExtractPlugin({
                filename: '[name].bundle.css'
            })
        ]
    }

    // FOOTNOTES...
    // [1] the output of CSS entries and JS entries need to be differentiated due to the
    // splitChunks issue described below
    // [2] splitChunks can create initial chunks that are empty after CSS extraction,
    // please see https://github.com/webpack/webpack/issues/7300

Edit webpack.dev.js

    const merge = require('webpack-merge');
    const common = require('./webpack.common.js');

    module.exports = merge(common, {
        mode: 'development'
    });

Edit webpack.prod.js

    const merge = require('webpack-merge');
    const common = require('./webpack.common.js');

    module.exports = merge(common, {
        mode: 'production'
    });

## Development

Create assets directories

    mkdir ultra/assets
    mkdir ultra/assets/css
    mkdir ultra/assets/img
    mkdir ultra/assets/js

Create image files

    touch ultra/assets/img/robot.svg
    touch ultra/assets/img/spider.svg
    touch ultra/assets/img/web.svg

Edit ultra/assets/img/robot.svg

    <svg width="234" height="234" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg"><title>Robot</title><path d="M70.18 43.98H29.82c-3.3 0-6 2.71-6 6v23.94c0 3.3 2.7 6 6 6h40.36c3.301 0 6-2.7 6-6V49.98c0-3.29-2.7-6-6-6zM36.44 66.99a5.036 5.036 0 0 1 0-10.07 5.036 5.036 0 1 1 0 10.07zm27.12 0a5.03 5.03 0 0 1-5.029-5.04c0-2.78 2.25-5.03 5.029-5.03a5.036 5.036 0 1 1 0 10.07zM55.49 22.733c0 2.06-1.13 3.85-2.811 4.79v13.63H47.32v-13.63a5.476 5.476 0 0 1-2.811-4.79c0-3.03 2.46-5.49 5.49-5.49s5.491 2.46 5.491 5.49zM20.82 51.145v21.62h-5.54c-3.3 0-6-2.699-6-6v-9.62c0-3.299 2.7-6 6-6h5.54z"/><path d="M20.82 51.145v21.62h-5.54c-3.3 0-6-2.699-6-6v-9.62c0-3.299 2.7-6 6-6h5.54z"/><g><path d="M90.72 57.145v9.62c0 3.301-2.7 6-6 6h-5.54v-21.62h5.54c3.3 0 6 2.701 6 6z"/><path d="M90.72 57.145v9.62c0 3.301-2.7 6-6 6h-5.54v-21.62h5.54c3.3 0 6 2.701 6 6z"/></g></svg>

Edit ultra/assets/img/spider.svg

    <svg width="32" height="32" viewBox="0 0 128 128" xmlns="http://www.w3.org/2000/svg"><path d="M112.03 60.69a6.23 6.23 0 0 0-3.16-2.53l-19.24-6.89 14.18-.73c2.1-.11 3.99-1.26 5.06-3.07 2.31-3.92 7.06-15.65 1.36-39.89a3.724 3.724 0 0 0-4.49-2.78 3.732 3.732 0 0 0-2.78 4.49c4.89 20.78 1.43 30.64-.22 33.83l-20.06 1.03v-.14c0-.2-.02-.39-.03-.59l10.49-3.64c2.29-.8 3.93-2.87 4.16-5.27.42-4.36.68-15.93-5.99-29.09a3.727 3.727 0 0 0-5.02-1.64 3.727 3.727 0 0 0-1.64 5.02c5.41 10.67 5.58 19.99 5.28 24.2l-9.56 3.32a14.284 14.284 0 0 0-6-5.15c-.01-2.2-.31-6.91-2.76-7.79-4.35-1.55-4.06 3.68-7.02 3.68-2.74 0-2.67-5.24-7.02-3.68-2.35.84-2.72 5.21-2.75 7.51-2.67 1.06-4.95 2.9-6.54 5.25L39.23 33c-.29-4.2-.12-13.52 5.28-24.2a3.735 3.735 0 0 0-6.66-3.38c-6.67 13.16-6.41 24.73-5.99 29.09a6.235 6.235 0 0 0 4.16 5.28l9.88 3.43c-.02.27-.04.53-.04.8v.11l-19.44-1c-1.65-3.19-5.1-13.04-.22-33.83.47-2.01-.77-4.02-2.78-4.49-2.01-.47-4.02.77-4.49 2.78-5.7 24.24-.95 35.98 1.36 39.89a6.223 6.223 0 0 0 5.06 3.07l14.18.73-19.24 6.89a6.18 6.18 0 0 0-3.16 2.53c-2.38 3.78-9.54 18.14-1.9 44.44.47 1.63 1.97 2.7 3.59 2.7a3.747 3.747 0 0 0 3.59-4.78c-6.62-22.79-.93-34.99.82-38l22.84-8.18c0 .03.01.06.01.08L31.65 70.15c-.85.78-1.47 1.78-1.79 2.9-1.17 4.09-6.53 25.7 2.5 48.9a3.739 3.739 0 0 0 3.48 2.38c.45 0 .91-.08 1.35-.26a3.749 3.749 0 0 0 2.13-4.84c-7.95-20.41-3.58-39.4-2.37-43.81l12.54-11.45a14.47 14.47 0 0 0 4.42 3.33c-5.09.9-9.37 4.55-10.85 9.6-1.29 4.41-1.98 10.63-.79 19.22 3.1 22.52 18.7 25.54 22.24 25.54 3.54 0 18.67-3.03 21.77-25.54 1.18-8.59.5-14.81-.79-19.22-1.48-5.05-5.75-8.7-10.84-9.6 1.81-.87 3.42-2.11 4.71-3.64l12.88 11.76c1.21 4.41 5.58 23.4-2.37 43.81-.75 1.92.2 4.09 2.13 4.84.45.17.9.26 1.35.26 1.5 0 2.91-.9 3.48-2.38 9.03-23.2 3.67-44.81 2.5-48.9a6.318 6.318 0 0 0-1.79-2.9l-14.6-13.34 23.02 8.24c1.76 3.01 7.44 15.2.82 38-.58 1.98.56 4.05 2.55 4.63.35.1.7.15 1.04.15 1.62 0 3.11-1.06 3.59-2.7 7.62-26.3.45-40.66-1.93-44.44z"/></svg>

Edit ultra/assets/img/web.svg

    <svg width="3130.667" height="3130.667" viewBox="0 0 3130.667 3130.667" xmlns="http://www.w3.org/2000/svg"><path d="M2067.994 276.112l-1.286.392C1728.61 379.46 1369.745 400.385 983.481 313.733l-1.381-.31-.698 1.231C796.45 640.9 562.896 918.003 241.799 1106.947l-1.327.78.481 1.463c127.403 386.832 131.014 745.828 37.24 1083.093l-.436 1.567 1.478.679c350.02 160.93 577.11 444.132 792.25 739.685l.815 1.12 1.317-.427c406.171-131.53 749.809-98.244 1083.27-37.204l1.275.233.688-1.1c196.29-314.04 431.55-589.345 739.727-792.414l1.113-.733-.304-1.297c-97.126-414.2-77.04-754.93-37.23-1083.536l.156-1.278-1.127-.623c-328.378-181.537-574.29-446.481-792.388-739.764zm-.158 6.177c217.004 291.39 461.854 554.884 788.183 736.154l-220.829 99.463C2381.595 962.355 2137.626 794.833 1983.557 510zm-3.728-1.016l-85.837 226.529c-292.683 85.737-589.238 101.052-890.655 30.644l-101.215-220.16c384.045 85.5 741.208 64.814 1077.707-37.013zm-87.53 230.997l-77.356 204.146c-235.748 76.609-474.101 102.765-718.324 24.934l-91.266-198.52c300.134 69.571 595.542 54.354 886.946-30.56zm5.217 2.49c154.207 283.148 397.143 450.337 649.188 605.04l-198.89 89.583c-193.85-130.293-385.03-264.744-525.873-490.43zm876.167 507.025c-39.447 326.801-59.096 666.223 36.9 1077.992l-226.676-85.893c-69.888-297.213-96.148-594.145-30.744-890.718zM982.862 319.8l99.614 221.163c-120.82 264.755-320.267 483.681-608.126 651.305l-227.16-84.074C566.144 919.515 798.646 643.852 982.863 319.8zm921.686 403.672c140.673 224.153 330.938 358.324 523.448 487.756l-204.854 92.268c-146.324-86.762-293.493-169.575-396.576-369.326zm-7.059-2.481l-79.365 209.448c-173.752 50.815-348.814 90.259-541.396 19.357l-93.676-203.761c242.979 76.518 480.222 50.645 714.438-25.044zm-3.607 20.296l-69.688 188.289-.402-.79-1.076.315zM2633.1 1125.16c-64.597 295.535-38.524 591.31 30.784 887.093l-204.316-77.42c-68.44-236.947-76.105-476.45-24.686-718.545zM1084.555 545.58l89.419 198.528c-114.52 212.988-246.899 411.76-490.084 525.714l-204.912-75.84c285.891-167.12 484.671-385.12 605.577-648.402zm737.28 387.757c.187.366.377.725.564 1.09l-71.473 193.112-.114-.267-.718-1.712-1.731.669-.401.154 73.059-192.809c.271-.08.543-.158.814-.237zm-5.405 1.574l-73.25 193.312c-119.83 46.141-239.752 86.484-377.75 14.515l-86.45-188.045c191.282 69.155 365.507 30.377 537.45-19.781zm8.343 4.104c102.942 197.404 249.337 280.597 394.112 366.4l-189.6 85.397c-133.877-42.736-222.12-132.482-276.182-258.153zm605.788 279.262c-50.697 240.79-43.068 479.139 24.549 714.869l-210.113-79.617c-64.992-171.202-77.234-350.611-18.628-541.378zm-20.262 5.112l-182.424 83.866.25-.803-.968-.575zm-1234.352-474.9l92.242 204.797c-77.476 172.305-192.512 311.296-369.406 396.071l-209.871-77.676c241.103-114.243 373.248-311.953 487.035-523.192zm11.886 17.088l83.833 182.353-.92-.349-.465 1.046zm1035.07 542.217c.227.136.456.27.684.406-.123.399-.243.797-.367 1.195l-187.104 86.018c.043-.113.077-.23.12-.344l.668-1.88-1.909-.585c-.078-.02-.153-.047-.23-.072zm-1.184 6.495c-57.354 189.166-45.33 367.475 18.506 537.433l-192.784-73.05c-52.836-128.11-55.921-254.03-13.076-378.25zm-473.703-183.835c.325.77.652 1.536.98 2.303l-60.988 164.78c-.11-.404-.225-.804-.333-1.209l-.633-2.373-2.144 1.198c-.374.21-.747.41-1.121.619l62.37-164.599 1.868-.719zm-6.65 2.559l-62.738 165.567c-88.202 47.336-166.457 47.174-237.414 9.003l-73.207-159.237c136.267 68.937 255.689 29.858 373.358-15.333zm9.824 4.815c53.998 123.289 141.576 212.166 272.92 255.316l-159.88 72.01c-89.875-29.05-150.28-80.79-174.45-161.404zm-477.269-184.994l85.952 186.96c-.031-.022-.065-.035-.098-.052l-1.99-1.078-.728 2.142c-.025.094-.06.184-.093.275l-84.585-187.796c.13-.292.262-.582.393-.874.383.145.767.28 1.15.423zm-3.636 5.104l84.859 188.404c-45.875 130.958-129.094 225.493-257.802 276.504l-193.574-71.644c174.843-84.835 289.275-222.867 366.517-393.264zm761.699 437.724c-.231.655-.46 1.311-.689 1.967l-159.372 73.269c.104-.46.2-.923.305-1.383l.393-1.73-1.697-.519c-.412-.126-.82-.257-1.231-.384l159.835-71.991c.817.258 1.635.516 2.455.77zm-2.399 6.957c-41.522 122.848-38.39 247.64 12.977 374.204l-163.394-61.913c-24.981-88.232-23.95-166.515-8.274-239.335zm-669.643-258.467c.853.456 1.706.907 2.557 1.355l73.074 158.948c-.364-.207-.729-.409-1.092-.618l-2.436-1.402-.406 2.781c-.061.414-.127.826-.19 1.24l-72.2-160.3c.231-.668.463-1.335.692-2.004zm-2.533 7.219l72.925 161.907c-12.972 73.229-56.101 134.605-162.817 172.552l-164.85-61.013c126.228-51.269 208.77-144.94 254.742-273.446zm327.223 148.29c.367 1.324.743 2.64 1.128 3.949l-47.531 128.424-2.251-2.101-3.078.106 48.793-128.768c.978-.53 1.959-1.07 2.939-1.61zm-8.052 4.325l-47.818 126.195-129.145 4.438-55.79-121.353c69.797 35.825 147.057 35.494 232.753-9.28zm11.026 5.64c25.115 77.934 84.728 128.865 171.466 157.852l-123.359 55.562-94.458-88.18zm180.519 160.768c-.187.834-.369 1.67-.552 2.504l-126.477 58.146-.105-3.077-2.251-2.102 125.717-56.624c1.217.39 2.44.772 3.668 1.153zm-1.555 7.169c-15.057 71.8-15.908 149.059 8.142 235.658l-129.035-48.894-4.438-129.145zm-431.573-169.046c1.09.612 2.182 1.215 3.276 1.81l57.207 124.435-3.078.106-2.101 2.25-56.003-124.339c.245-1.416.476-2.838.7-4.262zm-1.928 10.834l54.407 120.794-88.181 94.458-126.224-46.717c101.968-37.487 145.81-97.582 159.998-168.535zm201.46 114.453l2.263 2.113-60.346 163.047 158.519-71.398 2.263 2.113.107 3.094-157.963 72.621 162.576 61.603.106 3.095-2.113 2.264-163.047-60.346 71.399 158.519-2.113 2.263-3.095.107-72.62-157.963-61.604 162.576-3.095.106-2.263-2.113 60.345-163.047-158.518 71.399-2.264-2.113-.106-3.095 157.962-72.62-162.576-61.605-.106-3.095 2.113-2.263 163.047 60.346-71.398-158.52 2.113-2.263 3.095-.106 72.62 157.963 61.604-162.577zm5.29 4.938l92.12 85.998-148.746 66.997zm-12.524-4.69l-57.805 152.553-68.143-148.223zm110.182 102.86l4.329 125.948-152.552-57.806zm-248.301-92.992l66.996 148.745-152.994-56.625zm1404.168 659.397c-306.224 202.594-540.398 476.444-735.835 788.477l-99.531-220.98c156.44-241.08 325.67-475.282 608.047-651.63zm-231.833-85.804c-280.78 176.105-449.718 409.41-605.396 649.165l-89.76-199.284c118.385-208.856 280.306-385.22 490.66-525.568zm-226.94-88.066l-188.28-69.685.91-.451-.39-1.01zm18.024 10.743c-208.895 139.87-370.07 315.363-488.181 522.893l-92.373-205.086c67.433-160.565 177.22-299.223 369.583-395.89zm-210.558-83.698l.36.95c-.337.168-.67.338-1.006.507l-193.083-71.462.23-.091 1.835-.711-.766-1.813c-.05-.117-.097-.234-.146-.351zm-197.489-74.832c.262.631.525 1.263.79 1.894-.802.314-1.6.63-2.398.946l-164.477-60.875c.403-.14.796-.286 1.201-.426l1.72-.597-.515-1.747c-.134-.457-.257-.908-.39-1.364zm192.18 78.636c-189.882 96.334-299.381 234.005-366.831 392.94l-84.962-188.63c36.514-126.465 119.15-220.56 258.049-276.017zm-360.705-142.494c.23.799.453 1.595.686 2.394-1.282.452-2.565.904-3.826 1.364l-129.06-47.766 2.102-2.251-.106-3.078zm161.644 68.819c-135.894 55.396-218.051 148.527-255.135 272.647l-71.652-159.081c25.35-69.418 56.66-135.711 162.006-174.554zm-170.256-63.014c-101.296 38.79-133.828 104.16-158.768 171.615l-55.825-123.944 88.18-94.458zm-130.295-48.224l-85.998 92.12-66.997-148.745zm-340.647-137.753l-2.1 2.251.105 3.077-128.818-48.811a249.75 249.75 0 0 0-1.64-3.05c1.164-.399 2.33-.797 3.48-1.202zm-134.62-49.825c-.404.137-.8.278-1.204.415l-2.176.734 1.12 2.006c.207.372.406.746.612 1.12l-163.325-61.888a425.617 425.617 0 0 0-1.568-2.452c.691-.268 1.381-.537 2.07-.808zm289.191 118.564l-148.223 68.143-4.33-125.949zm-156.424-59.272l4.439 129.145-124.53 57.25c23.212-80.421 31.01-159.642-6.262-234.273zm-302.63-122.16c-.075.028-.148.06-.223.087l-2.207.845 1.289 1.98.049.076-192.477-72.933c-.147-.338-.29-.675-.437-1.013.333-.159.663-.32.995-.478zm170.963 72.268c39.373 75.558 31.182 155.963 6.982 238.4l-158.06 72.664c57.444-130.285 60.204-255.447-11.666-372.731zm307.854 127.614l68.144 148.223-125.949 4.329zm-13.702-5.133l-56.625 152.995-92.12-85.998zm-663.117-268.032l-.978.462.434.99-187.704-71.125zm200.357 81.661c74.659 118.63 71.794 244.872 12.283 377.367l-188.09 86.47c53.46-178.772 58.31-357.94-18.013-537.279zM895.55 1357.99c77.916 180.653 72.88 360.934 18.351 541.294l-202.9 93.28c86.438-226.834 78.994-464.834-24.897-713.937zm-214.362-81.226c105.475 250.913 112.811 490.038 24.85 718.08l-198.562 91.286c77.11-287.186 70.57-582.486-30.545-886.765zm-208.856-79.14c102.199 305.748 108.684 602.188 30.637 890.58L282.953 2189.35c92.54-335.71 88.757-693.037-37.14-1077.561zm1177.66 576.681l56.77 126.039c-.374 1.025-.746 2.05-1.116 3.075-1.047-.425-2.094-.85-3.138-1.262l-57.694-125.495 3.077-.105.54-.579zm-9.316 2.5l56.72 123.373c-88.345-33.498-164.623-24.287-233.96 7.99l48.095-126.925zm-241.479-96.425l.106 3.077 2.25 2.101-126.237 56.859a508.215 508.215 0 0 0-3.062-1.216c.285-.95.564-1.9.845-2.85zm5.383 8.004l94.458 88.18-46.742 126.292c-27.638-71.789-87.259-123.298-172.105-158.446zm102.813 93.001l-48.935 129.142c-.962.466-1.923.934-2.882 1.408a241.908 241.908 0 0 0-1.246-3.573l47.735-128.972 2.25 2.101zm201.619 123.954l71.87 159.568a455.71 455.71 0 0 0-.765 2.711l-1.786-.533-73.5-159.877c.403.164.804.323 1.208.488l1.847.76.676-1.879c.146-.414.301-.825.45-1.239zm-9.33-.186l73.786 160.498c-122.968-35.914-247.345-44.604-374.264 12.03l62.29-164.387c70.516-33.763 147.661-43.56 238.188-8.141zm-431.195-164.682c-.13.436-.256.871-.387 1.307l-.511 1.689 1.643.642c.412.167.823.328 1.237.49l-160.383 72.238-1.706-.785c.38-.832.757-1.664 1.133-2.496zm6.906 6.1c87.627 35.572 148.048 88.007 174.874 161.767l-60.509 163.487c-23.404-74.465-63.799-124.733-112.5-162.77-48.778-38.1-105.829-64.008-162.572-90.1zm176.85 167.43c.135.4.274.798.407 1.2l.692 2.1 1.977-.99c.387-.194.776-.376 1.163-.569l-62.207 164.17-1.822.841a400.3 400.3 0 0 0-.852-2.904zm331.079 156.322l84.554 187.73c-.16.382-.322.763-.481 1.146l-.83-.189-86.11-187.305.42.125 1.875.566.519-1.887c.013-.063.035-.124.053-.186zm-7.74-.073l86.286 187.686c-175.229-39.77-353.015-52.354-537.716 17.815l73.14-193.025c128.244-58.421 253.665-49.36 378.29-12.476zm-671.369-254.256c-.036.066-.062.134-.09.202l-.799 1.734 1.733.799c.137.064.275.126.412.189l-187.7 84.542c-.336-.145-.67-.292-1.008-.436l.298-.989zm5.855 5.041c57.755 26.576 115.682 52.635 164.84 91.03 49.252 38.468 89.742 89.27 112.673 165.645l-71.943 194.382c-74.372-153.138-195.892-280.672-393.124-366.582zm759.954 441.782l82.445 183.044-83.888-182.471 1.1.254zm-6.14-.503l93.45 203.272c-232.719-67.11-470.155-69.974-714.358 24.397l79.363-209.443c185.942-71.57 364.757-58.653 541.545-18.226zm-471.538-177.305l-72.973 192.58c-.309.12-.618.236-.927.356-.169-.354-.34-.707-.508-1.06l71.444-193.035c.01.03.016.055.024.083l.612 2.166 2.037-.957c.097-.046.193-.088.29-.134zm-479.921-180.842l-.313 1.017.941.4-183.053 82.45zm5.378 3.467c199.8 86.214 321.722 214.724 395.992 369.442l-78.26 211.449c-135.461-180.032-269.291-361.288-522.4-488.708zM1959.44 2470.17l91.158 198.283c-297.203-76.232-592.797-62.336-886.832 30.565l77.39-204.238c245.636-95.759 484.172-92.723 718.284-24.61zM708.65 2001.835c255.011 127.54 388.828 309.592 525.306 490.945l-75.785 204.763c-177.015-234.628-350.514-472.827-648.08-606.276zm608.904 280.546l-71.142 187.748 69.676-188.255.432.912zm735.095 390.536l101.314 220.375c-331.518-60.47-673.906-93.114-1077.746 36.77l85.825-226.5c295.377-93.846 592.124-107.815 890.608-30.646zm-1547.22-579.55c299.247 133.082 472.997 372.319 651.168 608.429l-84.125 227.295C858.66 2635.548 632.328 2353.97 284.625 2192.82z" fill="white"/></svg>

Create Sass files

    touch ultra/assets/css/itsy.scss
    touch ultra/assets/css/main.scss

Edit ultra/assets/css/main.scss

    // packaged styles

    @import '~normalize.css/normalize.css';  //www.npmjs.com/package/normalize.css

    // project styles

    body {
        font-family: monospace;
        font-size: 18px;
    }

Edit ultra/assets/css/itsy.scss

    .itsy {
        background-color: orange;
        background-image: url(../img/web.svg);
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
        border-bottom: solid 9px black;
        position: relative;
        margin: 0;
        &-bitsy {
            position: absolute;
            right: 9%;
            top: -9px;
            transform: rotate(225deg);
        }
        &-pixy {
            display: block;
            margin: auto;
        }
    }

Create itsy.js

    touch ultra/assets/js/itsy.js

Edit ultra/assets/js/itsy.js

    import { h, render } from 'preact';
    import spider from '../img/spider.svg';
    import '../css/itsy.scss';

    let crawlers = document.querySelectorAll('.js-itsy');
    let length = crawlers.length;
    let index = 0;

    for(; index < length; index++){
        render(<img class='itsy-bitsy' src={ spider } alt='' />, crawlers[index]);
    }

Create templates directory

    mkdir ultra/templates

Create index.html

    touch ultra/templates/index.html

Edit ultra/templates/index.html

    {% load static %}

    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Baa weep grahna weep ninny bong</title>
            <link rel="stylesheet" href="{% static 'css/main.bundle.css' %}">
            <link rel="stylesheet" href="{% static 'js/itsy.bundle.css' %}">
        </head>
        <body>
            <h1>
                {{ salutations }}
            </h1>
            <figure class="itsy js-itsy">
                <img class="itsy-pixy" src="{% static 'img/robot.svg' %}" alt="">
            </figure>
            <script src="{% static 'js/itsy.bundle.js' %}"></script>
        </body>
    </html>

Create views.py

    touch ultra/views.py

Edit ultra/views.py

    from django.shortcuts import render

    def index(request):
        context = {
            'salutations': 'Greetings programs!',
        }
        return render(request, 'index.html', context)

Edit ultra/urls.py

    from django.conf import settings
    from django.urls import include, path
    from . import views

    urlpatterns = [
        path('', views.index, name='index'),
    ]

    if settings.DEBUG:
        import debug_toolbar
        urlpatterns = [
            path('__debug__/', include(debug_toolbar.urls)),
        ] + urlpatterns

Create storage.py

    touch ultra/storage.py

Edit ultra/storage.py

    from django.contrib.staticfiles.storage import HashedFilesMixin, ManifestStaticFilesStorage

    class StaticFiles(ManifestStaticFilesStorage):

        patterns = HashedFilesMixin.patterns + (
            ("*.js", (
                # look for `"img/file.svg"` pattern and replace it with `"img/file.b4726a02aa6f.svg"`
                (r"""(['"](img\/.*?)['"])""", """\"%s\""""),
            )),
        )

        def hashed_name(self, name, content=None, filename=None):
            """
            when `collectstatic` is run, JS files are scanned for references to image files, e.g. "img/file.svg"
            since these image files are not referenced absolutely, e.g. "/static/img/file.svg",
            their filenames are returned within the context of their respective JS files, e.g. "js/img/file.svg"
            """
            if name.startswith("js/img"):
                name = name.replace("js/img", "img")
            try:
                result = super().hashed_name(name, content, filename)
            except ValueError:
                # when the file is missing, forgive and forget about it
                result = name
            return result

## Update django settings

Remove settings.py

    rm ultra/settings.py

Create settings directory

    mkdir ultra/settings

Create settings files

    touch ultra/settings/__init__.py
    touch ultra/settings/common.py
    touch ultra/settings/dev.py
    touch ultra/settings/prod.py

Edit ultra/settings/common.py

    import os

    BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))  # e.g. os.path.join(BASE_DIR, ...)

    INSTALLED_APPS = [
        'django.contrib.staticfiles',
        'ultra',
    ]

    LANGUAGE_CODE = 'en-us'

    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]

    ROOT_URLCONF = 'ultra.urls'

    STATIC_URL = '/static/'

    STATICFILES_DIRS = (
        # https://docs.djangoproject.com/en/2.1/ref/settings/#prefixes-optional
        ('img', os.path.join(BASE_DIR, 'assets/img')),
    )

    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [],
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                ],
            },
        },
    ]

    TIME_ZONE = 'UTC'

    USE_I18N = True

    USE_L10N = True

    USE_TZ = True

    WSGI_APPLICATION = 'ultra.wsgi.application'

Edit ultra/settings/dev.py

    from .common import *

    DEBUG = True

    INSTALLED_APPS += (
        'debug_toolbar',
    )

    INTERNAL_IPS = [
        '127.0.0.1',
    ]

    MIDDLEWARE += (
        'debug_toolbar.middleware.DebugToolbarMiddleware',
    )

    SECRET_KEY = 'a+unique+and+unpredictable+value'

Edit ultra/settings/prod.py

    import os
    from .common import *

    # e.g. ['.spiderwebrobot.com',]
    ALLOWED_HOSTS = ['localhost', '127.0.0.1', '[::1]']

    DEBUG = False

    SECRET_KEY = 'a+unique+and+unpredictable+value'

    STATIC_ROOT = os.path.join(BASE_DIR, '../static_root')

    STATICFILES_STORAGE = 'ultra.storage.StaticFiles'

Edit ultra/wsgi.py

    import os
    from django.core.wsgi import get_wsgi_application

    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ultra.settings.prod')

    application = get_wsgi_application()

## Review development

Compile assets

    ./node_modules/.bin/webpack --config webpack.dev.js

Start virtual environment

    source venv/bin/activate

Run server

    python manage.py runserver --settings=ultra.settings.dev

Navigate to...

    http://localhost:8000/

Stop running server

    (control + c)

Exit virtual environment

    deactivate

## Configure gunicorn

Create gunicorn.conf.py

    touch gunicorn.conf.py

Edit gunicorn.conf.py

    # reserve port 9000 for gunicorn
    bind = '127.0.0.1:9000'

    # errorlog = '/full_path/to/project/logs/gunicorn-error.log'
    # accesslog = '/full_path/to/project/logs/gunicorn-access.log'
    # loglevel = 'debug'

    workers = 1

## Configure nginx

Create nginx.conf

    touch nginx.conf

Edit nginx.conf

    server {
        # reserve port 80 for nginx
        listen 80;
        server_name localhost;

        # access_log /full_path/to/project/logs/access.log;
        # error_log /full_path/to/project/logs/error.log;

        location / {
            # pass traffic to the gunicorn server
            proxy_pass http://127.0.0.1:9000;
        }

        location /static {
            # e.g. (pwd + /static_root)
            alias /full_path/to/project/static_root;
        }
    }

Create servers directory (if not already created)

    mkdir /usr/local/etc/nginx/servers

Soft link nginx.conf

    # e.g. (pwd + /nginx.conf)
    ln -s /full_path/to/project/nginx.conf /usr/local/etc/nginx/servers/project-ultra

## Compile and collect assets

Compile assets

    ./node_modules/.bin/webpack --config webpack.prod.js

Start virtual environment

    source venv/bin/activate

Collect static files

    python manage.py collectstatic --clear --settings=ultra.settings.prod

Exit virtual environment

    deactivate

## Simulate production

Start virtual environment

    source venv/bin/activate

Start gunicorn

    gunicorn -c gunicorn.conf.py ultra.wsgi

Open new terminal

    (command + t)

Start nginx

    nginx

Navigate to...

    http://localhost/

Stop nginx

    nginx -s quit

Close new terminal

    exit

Stop gunicorn (inside previously opened terminal)

    (control + c)

Exit virtual environment

    deactivate

## Ignore files

Create .gitignore

    touch .gitignore

Edit .gitignore

    __pycache__
    node_modules
    static
    static_root
    venv

## Resources

* [Configuring Django with React](https://medium.com/labcodes/configuring-django-with-react-4c599d1eae63)
* [Deploy Django + nginx + gunicorn on Mac OSX (Part 1)](http://cheng.logdown.com/posts/2015/01/27/deploy-django-nginx-gunicorn-on-mac-osx)
* [Deploying Gunicorn](http://docs.gunicorn.org/en/latest/deploy.html)
* [Deploying static files](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)
* [Django Best Practice: Settings file for multiple environments](https://medium.com/@ayarshabeer/django-best-practice-settings-file-for-multiple-environments-6d71c6966ee2)
* [Django Debug Toolbar](https://django-debug-toolbar.readthedocs.io/en/latest/installation.html#getting-the-code)
* [django.contrib.staticfiles.storage](https://github.com/django/django/blob/master/django/contrib/staticfiles/storage.py)
* [Make Django's collectstatic command forgiving](https://timonweb.com/tutorials/make-djangos-collectstatic-command-forgiving/)
