pipeline {
  agent any

  environment {
        GIT_NAME = "eea.alchemy"
        GIT_SRC = "https://github.com/eea/$GIT_NAME.git --branch=$BRANCH_NAME"
    }

  stages {
    stage('Tests') {
      steps {
        parallel(

          "WWW": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-www"
docker run -i --net=host --name="$NAME" -e GIT_NAME="$GIT_NAME" -e GIT_BRANCH="$BRANCH_NAME" eeacms/www-devel /debug.sh bin/test -v -vv -s $GIT_NAME
docker rm -v $NAME'''
            }
          },

          "KGS": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-kgs"
docker run -i --net=host --name="$NAME" -e GIT_NAME="$GIT_NAME" -e GIT_BRANCH="$BRANCH_NAME" eeacms/kgs-devel /debug.sh bin/test --test-path /plone/instance/src/$GIT_NAME -v -vv -s $GIT_NAME
docker rm -v $NAME'''
            }
          },

          "Plone4": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-plone4"
docker run -i --net=host --name="$NAME" -v /plone/instance/parts -e GIT_BRANCH="$BRANCH_NAME" -e ADDONS="$GIT_NAME" -e DEVELOP="src/$GIT_NAME" eeacms/plone-test -v -vv -s $GIT_NAME
docker rm -v $NAME'''
            }
          }
        )
      }
    }

    stage('Code Analysis') {
      steps {
        parallel(

          "ZPT Lint": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-zptlint"
docker run -i --net=host --name="$NAME" -e GIT_SRC="$GIT_SRC" eeacms/zptlint
docker rm -v $NAME'''
            }
          },

          "JS Lint": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-jslint"
docker run -i --net=host --name="$NAME" -e GIT_SRC="$GIT_SRC" eeacms/jslint4java
docker rm -v $NAME'''
            }
          },

          "CSS Lint": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-csslint"
docker run -i --net=host --name="$NAME" -e GIT_SRC="$GIT_SRC" eeacms/csslint
docker rm -v $NAME'''
            }
          },

          "PyFlakes": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-pyflakes"
docker run -i --net=host --name="$NAME" -e GIT_SRC="$GIT_SRC" eeacms/pyflakes
docker rm -v $NAME'''
            }
          },

          "PyLint": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-pylint"
docker run -i --net=host --name="$NAME" -e GIT_SRC="$GIT_SRC" eeacms/pylint
docker rm -v $NAME'''
            }
          },

          "i18n": {
            node(label: 'docker-1.13') {
              sh '''
NAME="$BUILD_TAG-i18n"
docker run -i --net=host --name=$NAME -e GIT_SRC="$GIT_SRC" eeacms/i18ndude
docker rm -v $NAME'''
            }
          },

        )
      }
    }
  }
}
