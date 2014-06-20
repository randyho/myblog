---
layout: blog
title:  "Vim的DB2 SQL PL高亮"
categories: Coding
tags: Vim DB2
---


最近要写存储过程，公司用的是DB2，连过程的后缀都保存成.db2，搞得vim不能正常高亮。于是自己整理了一下DB2的关键词，弄了个DB2 SQL PL的高亮文件。

<!--more-->

把下面这行加到自己的vimrc

     ```vimrc
     au BufRead,BufNewFile *.db2 set filetype=db2pl
     ```

把下面的内容保存成db2pl.vim，放到vimfiles/syntax下就可以了。

    ```vimrc
    " Vim syntax file
    " Language:    db2pl (DB2 Procedures Language)
    " Maintainer:  Randy Ho <randyho AT 126.com>
    " Last Change: 2012 Sep 6
    
    " Description: Special for DB2
    "
    " For version 5.x: Clear all syntax items
    " For version 6.x: Quit when a syntax file was already loaded
    if version < 600
        syntax clear
    elseif exists("b:current_syntax")
        finish
    endif
    
    syn case ignore
    
    " DB2 Macro
    syn keyword sqlMacro     sqlcode sqlstate
    
    " DB2 Constant
    syn keyword sqlConstant  SYSCAT SYSFUN SYSIBM SYSSTAT SYSPROC
    
    " common functions
    syn keyword sqlFunction  count sum avg min max correlation covariance
    syn keyword sqlFunction  stddev variance absval sqrt power bigint mod
    syn keyword sqlFunction  years months weeks days day month year
    syn keyword sqlFunction  hours minutes seconds second minute hour
    syn keyword sqlFunction  dayname dayofweek dayofweek_iso dayofyear julian_day
    syn keyword sqlfunction  midnight_seconds monthname timestamp_iso timestamp_format
    syn keyword sqlfunction  timestampdiff to_char to_date week week_iso
    syn keyword sqlFunction  plan round row_number over
    syn keyword sqlFunction  graphical_ulplan long_ulplan value
    syn keyword sqlFunction  grouping rank dense_rank posstr
    syn keyword sqlFunction  acos asin atan cast ceiling convert cos cot
    
    " string functions
    syn keyword sqlFunction  substr substring length
    syn keyword sqlFunction  ascii char chr left ltrim repeat translate
    syn keyword sqlFunction  space right rtrim trim lcase ucase concat
    syn keyword sqlFunction  locate charindex patindex replace
    
    syn keyword sqlFunction  acos asin atan atn2 ceiling
    
    " keywords
    syn keyword sqlKeyword   absolute action add after aggregate
    syn keyword sqlKeyword   alter and any as asc
    syn keyword sqlKeyword   at audit authorization before between
    syn keyword sqlKeyword   both bufferpool by cache cascade
    syn keyword sqlKeyword   catalog check class cluster
    syn keyword sqlKeyword   collation column comment concat connection
    syn keyword sqlKeyword   constraint constraints continue cross
    syn keyword sqlKeyword   cube current cursor data
    syn keyword sqlKeyword   database current_timestamp current_user default defaults
    syn keyword sqlKeyword   deferred definition desc deterministic distinct
    syn keyword sqlKeyword   do domain dynamic each editproc
    syn keyword sqlKeyword   else elseif end encoding erase
    syn keyword sqlKeyword   escape every  exception exec
    syn keyword sqlKeyword   exists external false fieldproc file
    syn keyword sqlKeyword   first foreign full function go
    syn keyword sqlKeyword   global group handler having hold
    syn keyword sqlKeyword   host hours identity ignore immediate
    syn keyword sqlKeyword   in index inner inout insensitive
    syn keyword sqlKeyword   into is isolation jar java
    syn keyword sqlKeyword   join key language last lateral
    syn keyword sqlKeyword   left level like limit local
    syn keyword sqlKeyword   long match minutes mode modify
    syn keyword sqlKeyword   national natural new next no
    syn keyword sqlKeyword   none not null nulls object
    syn keyword sqlKeyword   of off old on only
    syn keyword sqlKeyword   optimization option or order out
    syn keyword sqlKeyword   outer package part partial partition
    syn keyword sqlKeyword   path plan precision prefix preserve partitioning
    syn keyword sqlKeyword   primary prior priqty privileges procedure
    syn keyword sqlKeyword   public recursive references referencing relative
    syn keyword sqlKeyword   rename reset restrict result returns
    syn keyword sqlKeyword   role rollup row rows  specific
    syn keyword sqlKeyword   schema scope scroll seconds secqty
    syn keyword sqlKeyword   security sensitive session sets simple
    syn keyword sqlKeyword   size some sqlid statement stogroup
    syn keyword sqlKeyword   subpages table temporary then to
    syn keyword sqlKeyword   transaction true type unique union
    syn keyword sqlKeyword   unknown user using validproc
    syn keyword sqlKeyword   values varchar variable varying vcat
    syn keyword sqlKeyword   view when where with without
    syn keyword sqlKeyword   work write character dec
    
    syn keyword sqlOperator  in any some all between exists
    syn keyword sqlOperator  like escape except not is and or
    syn keyword sqlOperator  intersect prior
    
    " The DB2 reserved statement
    syn keyword sqlStatement allocate alter begin call case
    syn keyword sqlStatement close commit connect
    syn keyword sqlStatement create deallocate declare delete describe
    syn keyword sqlStatement disconnect drop execute exit fetch
    syn keyword sqlStatement for from get goto grant if
    syn keyword sqlStatement input insert leave lock loop
    syn keyword sqlStatement open output parameter parameters
    syn keyword sqlStatement prepare read release resignal
    syn keyword sqlStatement return revoke rollback savepoint select
    syn keyword sqlStatement set signal start system trigger update
    syn keyword sqlStatement whenever while
    
    " The DB2 reserved type
    syn keyword sqlType  char long varchar
    syn keyword sqlType  decimal double float int integer numeric
    syn keyword sqlType  smallint real bit
    syn keyword sqlType  date time timestamp
    syn keyword sqlType  binary nchar
    
    " Strings and characters:
    syn region sqlString        start=+"+    end=+"+ contains=@Spell
    syn region sqlString        start=+\'+    end=+\'+ contains=@Spell
    
    " Numbers:
    syn match sqlNumber     "-\\=\\<\\d*\\.\\=[0-9_]\\>"
    
    " Comments:
    syn region sqlDashComment   start=/--/ end=/$/ contains=@Spell
    syn region sqlSlashComment  start=/\\/\\// end=/$/ contains=@Spell
    syn region sqlMultiComment  start="/\\*" end="\\*/" contains=sqlMultiComment,@Spell
    syn cluster sqlComment  contains=sqlDashComment,sqlSlashComment,sqlMultiComment,@Spell
    syn sync ccomment sqlComment
    syn sync ccomment sqlDashComment
    syn sync ccomment sqlSlashComment
    
    " Define the default highlighting.
    " For version 5.7 and earlier: only when not done already
    " For version 5.8 and later: only when an item doesn\'t have highlighting yet
    if version >= 508 || !exists("did_sql_syn_inits")
        if version < 508
            let did_sql_syn_inits = 1
            command -nargs=+ HiLink hi link <args>
        else
            command -nargs=+ HiLink hi link <args>
        endif
    
        HiLink sqlDashComment   Comment
        HiLink sqlSlashComment  Comment
        HiLink sqlMultiComment  Comment
        HiLink sqlNumber            Number
        HiLink sqlOperator          Operator
        HiLink sqlSpecial           Special
        HiLink sqlKeyword           Keyword
        HiLink sqlStatement         Statement
        HiLink sqlString            String
        HiLink sqlType          Type
        HiLink sqlFunction          Function
        HiLink sqlOption            PreProc
            HiLink sqlMacro             Include
            HiLink sqlConstant      Constant
    
        delcommand HiLink
    endif
    
    let b:current_syntax = "db2pl"
    ```
