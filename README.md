# InfluxDB Plugin for Jenkins

[![Jenkins Plugin](https://img.shields.io/jenkins/plugin/v/influxdb.svg)](https://plugins.jenkins.io/influxdb)
[![Jenkins.io Build Status](https://ci.jenkins.io/buildStatus/icon?job=Plugins%2Finfluxdb-plugin%2Fmaster)](https://ci.jenkins.io/job/Plugins/job/influxdb-plugin/job/master/)
[![Jenkins Plugin Installs](https://img.shields.io/jenkins/plugin/i/influxdb.svg?color=blue)](https://plugins.jenkins.io/influxdb)

## Description

Collects data from various other Jenkins plugins and sends metrics to InfluxDB. It's also
possible to send custom data inside pipeline jobs.

> :warning: From version 1.13 onwards different plugins are listed as optional
dependencies. In order to get rid of mandatory dependency errors,
the InfluxDB plugin must be re-installed.

> :warning: Breaking changes in 2.0
> - From version 2.0 onwards `selectedTarget` is a **mandatory** parameter
for pipelines and the `target` parameter is no longer supported.
> - Configuration As Code: the configuration needs to be changed from
`influxDbPublisher` to `influxDbGlobalConfig`.
> - Might cause issues when creating new targets in pipelines. The
`InfluxDbPublisher` instance is now
under jenkinsci.plugins.influxdb.**InfluxDbStep**.DescriptorImpl.


## Supported Metrics

All measurements share the following metrics:

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| build_number | integer | Build number |  |
| project_name | string | Build name |  |
| project_path | string | Build path |  |

### `jenkins_data`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| build_agent_name | string | Name of the executor node | 1.15 |
| build_exec_time | integer | Start time of the build | 1.17 |
| build_measured_time | integer | Time when InfluxDB plugin is called | 1.17 |
| build_result | string | SUCCESS, FAILURE, NOT BUILT, UNSTABLE, ABORTED, ?  | 1.10 |
| build_result_ordinal | integer | 0-5 in order of `build_result`. 5 is only for pipelines if build result is not set manually.  | 1.10 |
| build_scheduled_time | integer | Time when build was scheduled to run | 1.17 |
| build_status_message | string | Status message (stable, back to normal, broken since #50, etc.) | |
| build_successful | boolean | Boolean whether build succeeded | 1.10 |
| build_time | integer | Build execution time |  |
| last_stable_build | integer | Build number of the last stable build (0 if never) | 1.10 |
| last_successful_build | integer | Build number of the last successful build (0 if never) | 1.10 |
| project_build_health | integer | Health score from build | |
| tests_failed | integer | Amount of failed unit tests (from JUnit plugin) | |
| tests_skipped | integer | Amount of skipped unit tests (from JUnit plugin) | |
| tests_total | integer | total amount of unit tests (from JUnit plugin) | |
| time_in_queue | integer | Time build was in queue (from Metrics plugin | 1.16 |

### `cobertura_data`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| cobertura_branch_coverage_rate | float | Branch coverage percentage |  |
| cobertura_class_coverage_rate | float | Class coverage percentage |  |
| cobertura_line_coverage_rate | float | Line coverage percentage |  |
| cobertura_number_of_classes | float | Amount of classes |  |
| cobertura_number_of_packages | float | Amount of packages |  |
| cobertura_number_of_sourcefiles | float | Amount of source files |  |
| cobertura_package_coverage_rate | float | Package coverage percentage |  |

### `rf_results`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| rf_critical_failed | integer | Amount of failed critical tests |  |
| rf_critical_pass_percentage | float | Percentage of passed critical tests |  |
| rf_critical_passed | integer | Amount of passed critical tests |  |
| rf_critical_total | integer | Total amount of critical tests |  |
| rf_duration | integer | Test execution duration |  |
| rf_failed | integer | Amount of failed tests |  |
| rf_pass_percentage | float | Percentage of passed tests |  |
| rf_passed | integer | Amount of passed tests |  |
| rf_suites | integer | Amount of test suites |  |
| rf_total | integer | Total amount of tests |  |

### `suite_result`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| rf_critical_failed | integer | Amount of failed critical tests |  |
| rf_critical_passed | integer | Amount of passed critical tests |  |
| rf_critical_total | integer | Total amount of critical tests |  |
| rf_duration | integer | Test execution duration |  |
| rf_failed | integer | Amount of failed tests |  |
| rf_passed | integer | Amount of passed tests |  |
| rf_suite_name | string | Name of the test suite |  |
| rf_testcases | integer | Total amount of tests (including child suites) |  |
| rf_total | integer | Amount of tests in this suite |  |

### `tag_point`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| rf_critical_failed | integer | Amount of failed critical tests |  |
| rf_critical_passed | integer | Amount of passed critical tests |  |
| rf_critical_total | integer | Total amount of critical tests |  |
| rf_duration | integer | Test execution duration |  |
| rf_failed | integer | Amount of failed tests |  |
| rf_passed | integer | Amount of passed tests |  |
| rf_total | integer | Total amount of tests |  |
| rf_tag_name | string | Test tag name | 1.11 |

### `testcase_point`

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| rf_critical_failed | integer | 0 or 1 |  |
| rf_critical_passed | integer | 0 or 1 |  |
| rf_duration | integer | Test case execution duration |  |
| rf_failed | integer | 0 or 1 |  |
| rf_name | string | Name of the test case |  |
| rf_passed | integer | 0 or 1 |  |
| rf_suite_name | string | Name of the suite of the test case |  |

### `jacodo_data` (since 1.7)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| jacoco_brach_coverage_rage | float | Branch coverage percentage | |
| jacoco_brach_covered | integer | Amount of branches covered | 2.1 |
| jacoco_brach_missed | integer | Amount of branches missed | 2.1 |
| jacoco_class_coverage_rage | float | Class coverage percentage | |
| jacoco_class_covered | integer | Amount of classes covered | 2.1 |
| jacoco_class_missed | integer | Amount of classes missed | 2.1 |
| jacoco_complexity_coverage_rage | float | Complexity coverage percentage | |
| jacoco_complexity_covered | integer | Amount of complexity covered | 2.1 |
| jacoco_complexity_missed | integer | Amount of complexity missed | 2.1 |
| jacoco_instruction_coverage_rage | float | Instruction coverage percentage | |
| jacoco_instruction_covered | integer | Amount of instructions covered | 2.1 |
| jacoco_instruction_missed | integer | Amount of instructions missed | 2.1 |
| jacoco_line_coverage_rage | float | Line coverage percentage | |
| jacoco_line_covered | integer | Amount of lines covered | 2.1 |
| jacoco_line_missed | integer | Amount of lines missed | 2.1 |
| jacoco_method_coverage_rage | float | Method coverage percentage | |
| jacoco_method_covered | integer | Amount of methods covered | 2.1 |
| jacoco_method_missed | integer | Amount of methods missed | 2.1 |

### `performance_data` (since 1.10.1)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| 90percentile | integer | Point when 90 percentile was reached | 1.12 |
| average | float | Average performance (total duration / size) | |
| error_count | integer | Amount of failed samples | |
| error_percent | integer | Percentage of failed samples | |
| max | integer | Maximum duration | |
| median | integer | Median duration | 1.12 |
| min | integer | Minimum duration | |
| size | integer | Amount of samples | |
| total_traffic | integer | Total traffic in KB | |

### `sonarqube_data` (since 1.11)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| blocker_issues | float | Total amount of blocker issues | |
| branch_coverage | float | Branch coverage | 2.2 |
| bugs | float | Total amount of bugs | 2.2 |
| code_smells | float | Total amount of code smells | 2.2 |
| complexity | float | Total amount of complexity | 2.2 |
| coverage | float | Overall coverage | 2.2 |
| critical_issues | float | Total amount of critical issues | |
| display_name | string | Build display name | |
| duplicated_lines_density | float | Percentage of duplicated lines | 2.2 |
| info_issues | float | Total amount of info issues | |
| line_coverage | float | Line coverage | 2.2 |
| lines_of_code | float (integer until 2.2) | Total amount of lines (including comments) | |
| lines_to_cover | float | Total amount of lines to cover (excluding comments) | 2.2 |
| major_issues | float | Total amount of major issues | |
| minor_issues | float | Total amount of minor issues | |
| vulnerabilities | float | Total amount of vulnerabilities | 2.2 |

### `changelog_data` (since 1.12)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| affected_paths | string | Comma-separated list of changed files | |
| commit_count | integer | Amount of commits since last change set | |
| commit_messages | string | Comma-separated list of commit messages | |
| culprits | string | Comma-separated list of commit authors | |

### `perfpublisher_summary` (since 1.13)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| number_of_tests | integer | Total amount of tests | |
| number_of_executed_tests | integer | Amount of executed tests | |
| number_of_not_executed_tests | integer | Amount of not executed tests | |
| number_of_passed_tests | integer | Amount of passed tests | |
| number_of_failed_tests | integer | Amount of failed tests | |
| number_of_success_tests | integer | Amount of succeeded tests | |
| number_of_true_false_tests | integer | Amount of true-false tests | |
| best_compile_time_test_value | float | Best compilation time test value | |
| best_compile_time_test_name | string | Best compilation time test name | |
| worst_compile_time_test_value | float | Worst compilation time test value | |
| worst_compile_time_test_name | string | Worst compilation time test name | |
| average_compile_time | float | Average compilation test time | |
| best_performance_test_value | float | Best performance test value | |
| best_performance_time_test_name | string | Best performance time test name | |
| worst_performance_time_test_value | float | Worst performance time test value | |
| worst_performance_time_test_name | string | Worst performance time test name | |
| average_performance_time | float | Average performance test time | |
| best_execution_time_test_value | float | Best execution time test value | |
| best_execution_time_test_name | string | Best execution time test name | |
| worst_execution_time_test_value | float | Worst execution time test value | |
| worst_execution_time_test_name | string | Worst execution time test name | |
| average_execution_time | float | Average execution test time | |

### `perfpublisher_metric` (since 1.13)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| metric_name | string | Metric name | |
| average | float | Average value | |
| best | float | Best value | |
| worst | float | Worst value | |

### `perfpublihser_test` (since 1.13)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| test_name | string | Test name | |
| successful | boolean | Was test successful | |
| executed | boolean | Was test executed | |
| message | boolean | Test message | |
| compile_time | float | Test compilation time | |
| execution_time | float | Test execution time | |
| performance | float | Test performance | |

### `perfpublisher_test_metric` (since 1.13)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| test_name | string | Test name | |
| metric_name | string | Test metric name | |
| value | float | Metric value |  |
| unit | string | Metric unit |  |
| relevant | boolean | Is metric relevant |  |

### `serenity_data` (since 2.1)

| Metric | Type | Explanation | Introduced in |
| --- | --- | --- | --- |
| serenity_results_average_test_duration | integer | Maximum average duration in milliseconds |  |
| serenity_results_counts_compromised | integer | Amount of compromised results |  |
| serenity_results_counts_error | integer | Amount of error results |  |
| serenity_results_counts_failure | integer | Amount of failed results |  |
| serenity_results_counts_ignored | integer | Amount of ignored results |  |
| serenity_results_counts_pending | integer | Amount of pending results |  |
| serenity_results_counts_skipped | integer | Amount of skipped results |  |
| serenity_results_counts_success | integer | Amount of success results |  |
| serenity_results_counts_total | integer | Total amount of results |  |
| serenity_results_max_test_duration | integer | Maximum test duration in milliseconds |  |
| serenity_results_min_test_duration | integer | Minimum test duration in milliseconds |  |
| serenity_results_percentages_compromised | integer | Percentage of compromised results |  |
| serenity_results_percentages_error | integer | Percentage of error results |  |
| serenity_results_percentages_failure | integer | Percentage of failure results |  |
| serenity_results_percentages_ignored | integer | Percentage of ignored results |  |
| serenity_results_percentages_pending | integer | Percentage of pending results |  |
| serenity_results_percentages_skipped | integer | Percentage of skipped results |  |
| serenity_results_percentages_success | integer | Percentage of success results |  |
| serenity_results_total_clock_duration | integer | Total test clock duration in milliseconds |  |
| serenity_results_total_test_duration | integer | Total test duration in milliseconds |  |
| serenity_tags_* | integer | Amount of tests for each tag |  |

## Configuration

### Via Jenkins UI

Create a database in InfluxDB and a user with access rights. In Jenkins,
go to *Manage Jenkins \> Configure System \> InfluxDB Targets* and click
"Add". Provide the database information. The "URL" parameter requires
the whole URL of the InfluxDB database, including the `http(s)://` and the
database port. Also, provide the retention policy you want the data to
be stored in InfluxDB (e.g. `15m` or `2d`). By default, it is `infinite`.
Exceptions generated by the InfluxDB plugin can also be
ignored by deselecting the "Expose Exceptions" checkbox.

![](doc/img/jenkins-configuration.png)

In your job, select "Publish build data to InfluxDB" from the post-build
actions.

![](doc/img/post-build-action.png)

### Via Jenkins Pipeline

From version 1.19 onwards, you can create and remove targets in pipelines directly.

```
// Get InfluxDB plugin descriptor (version < 2.0)
def influxdb = Jenkins.instance.getDescriptorByType(jenkinsci.plugins.influxdb.DescriptorImpl)

// version >= 2.0
def influxdb = Jenkins.instance.getDescriptorByType(jenkinsci.plugins.influxdb.InfluxDbStep.DescriptorImpl)

// Create target
def target = new jenkinsci.plugins.influxdb.models.Target()

// Set target details

// Mandatory fields
target.description = 'my-new-target'
target.url = 'http://influxdburl:8086'
target.username = 'my-username'

// version < 2.0
target.password = 'my-password'

// version >= 2.0
target.password = hudson.util.Secret.fromString('my-password')

target.database = 'my-database'

// Optional fields
target.retentionPolicy = '1d'                    // default = 'autogen'
target.jobScheduledTimeAsPointsTimestamp = true  // default = false
target.exposeExceptions = true                   // default = true
target.usingJenkinsProxy = true                  // default = false

// Add a target by using the created target object
influxdb.addTarget(target)
influxdb.save()

// Write stuff to InfluxDB
influxDbPublisher(selectedTarget: 'my-new-target')

// Remove a target by using the target description field value
influxdb.removeTarget('my-new-target')
influxdb.save()
```

## Usage

### Freestyle Jobs

Select the InfluxDB target you wish to publish the data to.

![](doc/img/select-influxdb-target.png)

From the "Advanced" tab you can choose to set a custom prefix for your `project_name` field,
a custom project name to be used instead of the default job name and custom fields and tags
for your `jenkins_data` metric.

![](doc/img/advanced-options.png)

### Pipelines

The plugin can be used by calling either the `influxDbPublisher()` or the `step()` function.

The `influxDbPublisher()` function is only supported from version 1.21 onwards.

**Pipeline syntax**

The only mandatory parameter is `selectedTarget`, which is the "Description" for your
target in the global configuration.

``` syntaxhighlighter-pre
influxDbPublisher(selectedTarget: 'my-target')
```

``` syntaxhighlighter-pre
step([$class: 'InfluxDbPublisher', selectedTarget: 'my-target'])
```

Optional parameters

- `customProjectName` (String) - custom project name
- `customPrefix` (String) - custom prefix for project name
- `customData` (Map) - custom fields in "jenkins_custom_data" measurement
- `customDataTags` (Map) - custom tags in "jenkins_custom_data" measurement
- `customDataMap` (Map) - custom fields in custom measurements
- `customDataMapTags` (Map) - custom tags in custom measurements
- `jenkinsEnvParameterField` (String) - custom fields in "jenkins_data" measurement (newline-separated KEY=VALUE pairs)
- `jenkinsEnvParameterTag` (String) - custom tags in "jenkins_data" measurement (newline-separated KEY=VALUE pairs)
- `measurementName` (String) - custom measurement name (replaces default "jenkins_data" and "jenkins_custom_data")

All `customData*` parameters contain custom data generated during the
build and not by the plugin, so they are not available in the snippet generator.


> :heavy_exclamation_mark: NOTE! Up to release 1.10.3, pipeline was configured with using the url and database.
>
>``` syntaxhighlighter-pre
>step([$class: 'InfluxDbPublisher',
>        target: 'http://127.0.0.1:8086,jenkins_db',
>        ...
>    ])
>```
> This form of configuration is not supported from version 1.11 onwards.

Pipelines don't have post-build actions, so the build result, build
ordinal, and the build success boolean will default to `"?"`, `5`,
and `false` respectively, unless set manually before calling `InfluxDbPublisher`.
Only the build result needs to be set manually, as the boolean value and ordinal are set
based on build result. Also, the build status will appear as `"?"` and the build
duration might be a little off, because the build is not actually finished.
If you want to get those pieces of information you need to configure the plugin
separately on each job as a post-build action. The jobs can be run with,
for example, the [Build Pipeline Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin)
to get data from all jobs to InfluxDB. Alternatively, you can insert the
information in your build manually inside your Groovy script.

``` syntaxhighlighter-pre
try {
    // Build things here
    if (currentBuild.result == null) {
        currentBuild.result = 'SUCCESS' // sets the ordinal as 0 and boolean to true
    }
} catch (err) {
    if (currentBuild.result == null) {
        currentBuild.result = 'FAILURE' // sets the ordinal as 4 and boolean to false
    }
    throw err
} finally {
    influxDbPublisher(selectedTarget: 'my-target')
}
```

## Custom Data

You can write custom data to InfluxDB like this:

```
def myFields = [:]
myFields['field_a'] = 11
myFields['field_b'] = 12
influxDbPublisher(selectedTarget: 'my-target', customData: myFields)
```

This adds the fields `field_a` and `field_b` with values `11` and `12` respectively to a measurement called `jenkins_custom_data`.

You can also add tags to this measurement with the `customDataTags` parameter.

Alternatively, you can write custom data to InfluxDB with a higher degree of customization like this:

```
def myFields1 = [:]
def myFields2 = [:]
def myCustomMeasurementFields = [:]
myFields1['field_a'] = 11
myFields1['field_b'] = 12
myFields2['field_c'] = 21
myFields2['field_d'] = 22
myCustomMeasurementFields['series_1'] = myFields1
myCustomMeasurementFields['series_2'] = myFields2
influxDbPublisher(selectedTarget: 'my-target', customDataMap: myCustomMeasurementFields)
```

This creates 2 measurements, `series_1` and `series_2`.
It adds the fields `field_a` and `field_b` with values `11` and `12` respectively to measurement `series_1`.
It adds the fields `field_c` and `field_d` with values `21` and `22` respectively to measurement `series_2`.

You can also add tags to your custom measurements with the `customDataMapTags` parameter.
You **must** use the same map keys as measurement names as in `customDataMap`.


## Contribution

Create a pull request to the `development` branch.
**No pull requests are merged directly to `master`**.
Comment your changes sufficiently and create appropriate tests.

If you have an idea for a new feature or you want to report a bug,
please use the [Jenkins issue tracker](https://issues.jenkins-ci.org/issues/?jql=project%20%3D%20JENKINS%20AND%20component%20%3D%20influxdb-plugin).
