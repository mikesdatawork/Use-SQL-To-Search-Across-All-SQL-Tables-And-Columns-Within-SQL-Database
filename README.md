![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Search Across All SQL Tables And Columns Within SQL Database
**Post Date: July 3, 2017**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>This little bit of SQL Logic will allow you to search for a value across all tables and columns. This is serves as an expansion on Vyaskn's search script (http://vyaskn.tripod.com). This will allow you to not only find the values, but replace, or delete them if needed.
Note: Wild card search is permitted. Example: '%find this%'</p>   


## SQL-Logic
```SQL
use [MyDatabase];
set nocount on
 
declare @find_this      varchar(255) = '%MyValue%'
 
if object_id('tempdb..#found') is not null
    drop table #found
if object_id('tempdb..#foundall') is not null
    drop table #foundall
 
create table #found     (columnname nvarchar(255), columnvalue nvarchar(3000))
create table #foundall  ([table_name] nvarchar(255), [column_name] nvarchar(255), [value] nvarchar(4000))
declare @tablename      nvarchar(256)
declare @columnname     nvarchar(128)
declare @find_this2     nvarchar(110)
set @tablename      = ''
set @find_this2     = quotename('%' + @find_this + '%','''')
 
while   @tablename is not null
begin
set @columnname     = ''
set @tablename      = 
(
select
    min(quotename(table_schema) + '.' + quotename(table_name))
from   
    information_schema.tables
where      
    table_type          = 'base table'
                    and quotename(table_schema) + '.' + quotename(table_name) > @tablename
                    and objectproperty(object_id(quotename(table_schema) + '.' + quotename(table_name)), 'ismsshipped') = 0) while 
    (@tablename is not null) and (@columnname is not null) begin
    set @columnname =(select min(quotename(column_name))
    from   
        information_schema.columns
    where      
        table_schema    = parsename(@tablename, 2)
                        and table_name  = parsename(@tablename, 1)
                        and data_type in ('char', 'varchar', 'nchar', 'nvarchar')
                        and quotename(column_name) > @columnname)
 
if @columnname is not null
begin
insert into #found
exec
('select ''' + @tablename + '.' + @columnname + ''', left(' + @columnname + ', 3630) from ' + @tablename + ' (nolock) ' +' where ' + @columnname + ' like ' + @find_this2) end end end
 
insert into #foundall
 select
    [table_name]        = reverse(substring(reverse(columnname),charindex('.',reverse(columnname))+1,len(columnname)))
,   [column_name]   = reverse(left(reverse(columnname), charindex('.', reverse(columnname)) - 1)) 
,   [value]         = columnvalue
from
    #found
order by
    [table_name]
,   [column_name] asc
 
-- get all found values
select * from #foundall
 
-- replace all found values
declare @replace_all_values     varchar(max)
set @replace_all_values     = ''
select  @replace_all_values     = @replace_all_values + 
' update table ' + [table_name] + ' set ' + [column_name] + ' = '' replace(' + [column_name] + ' replace, ''' + [value] + ''', ''MyNewValue'');' + char(10)
from    #foundall
 
 
-- delete rows with found values
/*
declare @delete_rows        varchar(max)
set @delete_rows        = ''
select  @delete_rows        = @delete_rows + 
' delete from ' + [table_name] + ' where ' + [column_name] + ' = ''' + [value] + ''';' + char(10)
from    #foundall
 
exec    (@delete_rows) --for xml path (''), type
*/
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

