# For use with [`PkgTemplates.jl`][pkgtemplates]

# open Julia

## Using PkgTemplates in julia REPL in pkg manager mode
## run the following code

```julia
using PkgTemplates

struct Replacements <: PkgTemplates.Plugin
    d::Dict{String, Any}
end
Replacements(; kwargs...) = Replacements(Dict(string(k) => v for (k, v) in pairs(kwargs)))

PkgTemplates.user_view(::PkgTemplates.Plugin, t::Template, ::AbstractString) =
    PkgTemplates.getplugin(t, Replacements).d

PkgTemplates.@with_kw_noshow struct Assignment <: PkgTemplates.FileTemplate
    file::String = "templates/assignment_template.jl"
end

PkgTemplates.source(p::Assignment) = p.file
PkgTemplates.destination(::Assignment) = "src/assignment.jl"

function templ(num, path)
    a = lpad(num, 2, "0")
    t = Template(
            user="wellesley-bisc195",
            authors="Kevin Bonham, PhD <kbonham@wellesley.edu>",
            dir=path,
            julia=v"1.4",
            plugins=[# other than defaults
                SrcDir(file="templates/module_template.jl"),
                Tests(file="templates/tests_template.jl", project=true),
                Readme(file="templates/readme_template.md"),
                Git(ssh=true),
                GitHubActions(),
                Documenter{GitHubActions}(),
                Replacements(; ASSIGNMENT=a),
                Assignment()
            ])

    # replace XX with lesson number
    t("Assignment$a")
end

templ(1, path = "/Users/ksc/repos/courses") # replace with assignment number and correct path
```

## example to make assignment 5
templ(5, "/Users/ksc/repos/courses")

## TODO

- [ ] add `test` and `src` template with assignment set-up

[pkgtemplates]: https://invenia.github.io/PkgTemplates.jl/dev/user/#PkgTemplates.GitHubActions
