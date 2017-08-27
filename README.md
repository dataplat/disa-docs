# disa-docs
Crowd-sourced Template for SQL Server Documentation as required by DISA

## tips & tricks
DISA requires a ton of documentation for their SQL Server STIGs. Even if you have resolved a vulnerability, if it is not documented well, it can be listed as an open finding.

While it is safest to document a response to every single finding, the following are most important

* Any vulnerability that lists the words _document_, _procedure_, _process_ or _policy_, must be documented
* Any variation from the stated check must be documented. For instance, if a check states that you must limit database size, but you instead choose to ensure there's enough space by using an Agent Job that detects disk space capacity, this must be documented*.
* Any use of third-party tools must be documented
* Screenshots are key. In lieu of Screenshots, exported CSV files are useful. The path to these CSV files must be documented and the permissions must be limited.

## make STIGs easier to navigate

Fortunately, STIGs are XML so you can easily convert them into PowerShell Custom Objects. This is the code I wrote after I saved the 2005, 2012 and 2014 Database and Instance XML files to the `C:\stigs` directory on my local drive.

````
$all = @()

foreach ($file in (Get-ChildItem C:\stigs\*.xml)) {
	$xml = Get-Content -Path $file.FullName
	foreach ($group in $xml.Benchmark.Group) {
		$rule = $group.Rule
		if ($rule.Version.StartsWith("SQL4")) {
			$sqlversion = "2014"
		}
		elseif ($rule.Version.StartsWith("SQL2")) {
			$sqlversion = "2012"
		}
		else {
			$sqlversion = "2005"
		}
		
		if ($file.FullName -match 'nstance') {
			$type = "Instance"
		}
		else {
			$type = "Database"
		}
		
		$all += [pscustomobject]@{
			Type	    = $type
			SqlVersion = $sqlversion
			VulnID	   = $group.id
			RuleID	   = $rule.id
			Severity   = $rule.severity
			Weight	   = $rule.weight
			Version    = $rule.version
			Title	   = $rule.title
			Description = ($rule.description -replace '<[^>]+>', '').TrimEnd("false")
			FixText    = $rule.fixtext.'#text'
			Check	   = $rule.check.'check-content'
		}
	}
}
$all
````

From here, you can pipe `$all` to `Export-CliXml` or to a SQL Server Datatable using [dbatools](https://dbatools.io)' [Out-DbaDataTable](https://dbatools.io/Out-DbaDataTable).

Or you can just import [the XML included in this repo](https://github.com/sqlcollaborative/disa-docs/blob/master/sqlstig.xml). 
