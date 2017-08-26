# disa-docs
Crowd-sourced Template for SQL Server Documentation as required by DISA

DISA requires a ton of documentation for their SQL Server STIGs. Even if you have resolved a vulnerability, if it is not documented well, it can be listed as an open finding.

While it is safest to document a response to every single finding, the following are most important

* Any vulnerability that lists the words _document_, _procedure_, _process_ or _policy_, must be documented
* Any variation from the stated check must be documented. For instance, if a check states that you must limit database size, but you instead choose to ensure there's enough space by using an Agent Job that detects disk space capacity, this must be documented*.
* Any use of third-party tools must be documented
* Screenshots are key. In lieu of Screenshots, exported CSV files are useful. The path to these CSV files must be documented and the permissions must be limited.
