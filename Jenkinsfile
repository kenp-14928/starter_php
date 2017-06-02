#!/usr/bin/env groovy

node('php') {
    stage("Build") {
        try {
            checkout scm
            sh 'git clean -dxf'
            sh 'composer install'
        } catch (e) {
            println 'Building project failed'
            throw e
        }
    }
    
    stage("Static analysis") {
        try {
            println 'Checking code against PSR2 standard (ignoring data directory in integration tests)'
            sh 'vendor/bin/phpcs'
        } catch (e) {
            echo 'Coding standard for PSR was violated. Please fix your violations and rebuild.'
            throw e
        }
    }
    
    stage("Tests - unit") {
        try {
            sh 'vendor/bin/phpunit -c phpunit.xml --testsuite unit --coverage-clover /tmp/coverage_unit.xml'
        } catch (e) {
            echo 'Unit test do not pass. Please fix your broken test cases'
            throw e
        }
        
        try {
            sh 'vendor/bin/coverage-check /tmp/coverage_unit.xml 100'
        } catch (e) {
            echo 'Unit test coverage is below 100%. Please add coverage.'
            throw e
        }
    }
    
    stage("Tests - integration") {
        try {
            sh 'vendor/bin/phpunit -c phpunit.xml --testsuite integration --coverage-clover /tmp/coverage_integration.xml'
        } catch (e) {
            echo 'Integration tests do no pass. Please fix your broken test cases'
            throw e
        }
        
        try {
            sh 'vendor/bin/coverage-check /tmp/coverage_integration.xml 95'
        } catch (e) {
            echo 'Integration test coverage is below 95%. Please add coverage.'
            throw e
        }
    } 
}
