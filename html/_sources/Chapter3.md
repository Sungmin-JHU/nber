# *nberlbid* Stata Program Code

```Stata

qui {

    capture program drop nberlbid
    program define nberlbid

    syntax , [yearstart(int 4)] [yearend (int 4)]

    global url https://data.nber.org/lbid
    clear
    gen deaths = .
    gen year = .
    save mortdata, replace

    forvalues i = `yearstart' / `yearend' {
        assert inrange(`yearstart', 1983, 2013)
        assert inrange(`yearend', 1983, 2013)
        use "${url}`i'/linkco`i'us_num", clear
        g deaths =1
        collapse (count) deaths
        gen year = `i'
        save yr`i', replace
        use mortdata, clear
        append using yr`i'
        save mortdata, replace

    }
    
    use mortdata, clear

    gen deaths1k = deaths / 1000

    #delimit ;
    line deaths1k year,
    lcolor(navy) lwidth(thick)
    title("Trends in infant mortality in the US", size(large) color(navy))
    xtit("Year")
    ytit("Deaths in thousands")
    xlabel(2003(2)2013)
    ylabel(22(1)29, angle(90) grid glcolor(ltbluishgray) glwidth(medium))
    graphregion(fcolor(ltbluishgray))
    ;
    #delimit cr

    end

nberlbid, yearstart(2003) yearend(2013)

    }
```