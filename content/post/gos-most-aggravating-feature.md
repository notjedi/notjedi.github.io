---
date: "2023-01-29T00:00:00Z"
title: Golang's most aggravating feature
tags:
  - programming
  - golang
  - thoughts
---


If you've used Golang for any of your projects, you would have encountered with one of the following
errors:

1. `imported and not used: "X"`
2. `"X" declared but not used`

Of course, it is a good feature to have because it tells you that you've got unused variables and
imports in your code. But the reason why it's infuriating is because it impedes your development
speed. I personally am new to Golang and I've been working on a project called
[gotem](https://github.com/notjedi/gotem) which is basically a glamorous clone of
[tremc](https://github.com/tremc/tremc). Like most programmers, I use the most powerful debugging
tool ever, the print statement. For me to use the print statement in golang, I'll have to import a
package named `fmt` and do `fmt.Println()` to print anything whatsoever. As I'm always adding and
commenting out or deleting `fmt.Println`'s all over the place, I end up facing the above mentioned
errors very frequently and in order for the code to run, I'll have to go remove the import statement
and then remember to add it again when I add another `fmt.Println` again. I thought maybe there is a
way to disable this check via a flag or something and ended up searching for possible
[solutions](https://stackoverflow.com/questions/19560334/how-to-disable-golang-unused-import-error),
but the "solutions" are not that convincing.
