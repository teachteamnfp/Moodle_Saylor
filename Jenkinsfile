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
        "branch" : 'MOODLE_32_STABLE',
        "dest" : 'mod/journal'
    ]

]

def BuildMoodleJob() {
    moodleJob = [
        "moodle" : (
            node {
                git([url: 'https://github.com/moodle/moodle.git', branch: 'MOODLE_31_STABLE'])
                stash([name: 'moodle'])
            }
            )
    ]

    return moodleJob
}

def BuildPluginsJobs(plugins) {
    def pluginsJobs = []
    for (int i = 0; i < plugins.size(); i++) {
        def integer = i
        pluginsJobs[integer] = [
            (plugins[integer].get("name")) : (
                node {
                    git([url: (plugins[integer].get("url")), branch: (plugins[integer].get("branch"))])
                    stash([name: (plugins[integer].get("name"))])
                }
            )
        ]
    }

    return pluginsJobs
}

try {
    stage('Stash Repos') {

        def Jobs = BuildPluginsJobs(plugins)

        Jobs << BuildMoodleJob()

        parallel Jobs

    }
    node {
        stage('Build') {
            deleteDir()
            checkout scm

            unstash name: 'moodle'

            sh 'mkdir -p theme/saylor'
            dir("theme/saylor") {
                unstash name: 'theme_saylor'
            }

            sh 'mkdir -p mod/journal'
            dir("mod/journal") {
                unstash name: 'mod/journal'
            }

            sh 'ls -halt'
            echo env.BRANCH_NAME
        }

    }
}

catch (exc) {
    echo "Caught: ${exc}"
}