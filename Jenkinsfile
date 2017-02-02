#!/usr/bin/env groovy

/* Comments */


//TODO: then build parallel array thingy, then unstash
// println(plugins[0].get("dest")) plugins.size()
def plugins = [
    [
        "name" : 'theme_saylor',
        "url" : 'https://github.com/saylordotorg/moodle-theme_saylor.git',
        "branch" : env.BRANCH_NAME,
        "dest" : 'theme/saylor'
    ],
    [
        "name" : 'mod_journal',
        "url" : 'https://github.com/dmonllao/moodle-mod_journal.git',
        "branch" : 'MOODLE_31_STABLE',
        "dest" : 'mod/journal'
    ]

]

def StashMoodle() {
    node {
        deleteDir()
        git([url: 'https://github.com/moodle/moodle.git', branch: 'MOODLE_31_STABLE'])
        stash([name: 'moodle'])
    }
}

def StashPlugins(plugins) {
    for (int i = 0; i < plugins.size(); i++) {
        def integer = i
        node {
            deleteDir()
            git([url: (plugins[integer].get("url")), branch: (plugins[integer].get("branch"))])
            echo("Stashing: ${plugins[integer].get("name")}")
            stash([name: (plugins[integer].get("name"))])
        }
    }
}



try {
    stage('Stash Repos') {
        echo("Beginning stashing operations")

        StashMoodle()
        StashPlugins(plugins)

        echo("Finished stashing operations")
    }
    stage('Build') {
        node {
            deleteDir()
            echo("Checking out SCM")
            checkout scm

            echo("Beginning unstash operations")
            unstash name: 'moodle'

            sh 'mkdir -p theme/saylor'
            dir("theme/saylor") {
                unstash name: 'theme_saylor'
            }

            sh 'mkdir -p mod/journal'
            dir("mod/journal") {
                unstash name: 'mod_journal'
            }

        }

    } 
    stage('Test - Create Test DB') {
        node {
            sh 'ls -halt'
        }

    }
    stage('Test - Run Upgrade') {

    }
}

catch (exc) {
    echo "Caught: ${exc}"
}