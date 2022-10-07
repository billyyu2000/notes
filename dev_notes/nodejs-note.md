
# Nodejs Note

## npm scripts

- pm 允许在package.json文件里面，使用scripts字段定义脚本命令。
    ```json
    {
        // ...
        "scripts": {
            "build": "node build.js"
        }
    }
    ```

- 命令行下使用npm run命令，就可以执行这段脚本。
    ```bash
    $ npm run build  # 等同于执行 node build.js
    ```
- npm run 会新开一个Shell，并将当前目录的`node_modules/.bin/` 目录加入PATH 变量


## Promise

- 2 results
    - resolve
    - reject
- Promise takes a function with 2 parameters as its (Promise's) paremeter
    ```javascript
    let p = new Promise((resolve, reject) => {
        let a = 1 + 1
        if (a == 2) {
            resolve('Success')
        } else {
            reject('Failed')
        }
    });
    ```
- how we actually interact with promise ?
    - all we have to do is say anything inside `.then()`  is going to run for `resolve`
        ```javascript
        p.then((message) => {
            console.log('This is in the then ' + message)
        } )
        ;
        ```
    - but, to be able to catch an error, we need use the `.catch()` version of this
        ```javascript
        p.then((message) => {
            console.log('This is in the then ' + message)
        } )
        .catch((message) => {
            console.log('This is in the catch ' + message)
        })
        ;
        ```
        ```bash
        # result
        This is in the then Success
        ```

----

- let's look at an example of how we can take something that uses callbacks which is what promises are meant to replace and actually replace it with promises and it's a lot easier than it sounds.
    ```javascript
    const userLeft = false;
    const userWatchingCatMeme = false;

    // takes 2 arguments, a success callback and a failure callback
    function watchTutorialCallback(callback, errorCallback) {
        if (userLeft) {
            errorCallback({
            name: 'User Left',
            message: ':('
            });
        } else if (userWatchingCatMeme) {
            errorCallback({
            name: 'User Watching Cat Meme',
            message: 'WebDevSimplified < Cat'
            });
        } else {
            callback('Thumbs up and Subscribe');
        }
    }

    watchTutorialCallback(
        (message) => {
            console.log('Success: ' + message);
        } ,
        (error) => {
            console.log(error.name + ' ' + error.message);
        }
    );  
    ```
- now let's implement this using promises instead of callbacks becauses this is what promises were really meant to solve.
    1. let's new function name `watchTutorialCallback` to `watchTutorialPromise`
    2. completely remote those callback function parameters , because using promises we no longer have these callbacks
        ```javascript
        function watchTutorialPromise() {
        ```
    3. all we need to do inside of here is return a promise.
        ```javascript
        return new Promise((resolve, reject) => {

        });
        ```
    4. and inside of that function we just want to define all of our code that was calling these callbacks, and rename the callback functions to resolve and reject
        ```javascript
        function watchTutorialPromise() {
            return new Promise((resolve, reject) => {
                // just copy and paste the code from the callback function
                // and rename the callback functions to resolve and reject
                if (userLeft) {
                    reject({
                    name: 'User Left',
                    message: ':('
                    });
                } else if (userWatchingCatMeme) {
                    reject({
                    name: 'User Watching Cat Meme',
                    message: 'WebDevSimplified < Cat'
                    });
                } else {
                    resolve('Thumbs up and Subscribe');
                }
            });
        }
        ```
- use this function
    ```javascript
    watchTutorialPromise().then(  // <- watchTutorialCallback(
        (message) => {
            console.log('Success: ' + message);
        }) // <-  },
        .catch(     // add
        (error) => {
            console.log(error.name + ' ' + error.message);
        }
    );
    ```


<details>
<summary>
<b>entire program</b>
</summary>

```javascript
const userLeft = false;
const userWatchingCatMeme = false;

// takes 2 arguments, a success callback and a failure callback
function watchTutorialCallback(callback, errorCallback) {
    if (userLeft) {
        errorCallback({
        name: 'User Left',
        message: ':('
        });
    } else if (userWatchingCatMeme) {
        errorCallback({
        name: 'User Watching Cat Meme',
        message: 'WebDevSimplified < Cat'
        });
    } else {
        callback('Thumbs up and Subscribe');
    }
}

watchTutorialCallback(
    (message) => {
        console.log('Success: ' + message);
    } ,
    (error) => {
        console.log(error.name + ' ' + error.message);
    }
);


function watchTutorialPromise() {
    return new Promise((resolve, reject) => {
        // just copy and paste the code from the callback function
        // and rename the callback functions to resolve and reject
        if (userLeft) {
            reject({
            name: 'User Left',
            message: ':('
            });
        } else if (userWatchingCatMeme) {
            reject({
            name: 'User Watching Cat Meme',
            message: 'WebDevSimplified < Cat'
            });
        } else {
            resolve('Thumbs up and Subscribe');
        }
    });
}

watchTutorialPromise().then(  // <- watchTutorialCallback(
    (message) => {
        console.log('Success: ' + message);
    }) // <-  },
    .catch(     // add
    (error) => {
        console.log(error.name + ' ' + error.message);
    }
);
```

</details>


// https://www.youtube.com/watch?v=DHvZLI7Db8E

