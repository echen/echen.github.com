---
date: '2012-01-17 10:28:55'
layout: post
slug: quick-introduction-to-ggplot2
status: publish
title: Quick Introduction to ggplot2
wordpress_id: '660'
tags:
- ggplot2
- r
---

For a much better-looking version of this post, see my [Github repository](https://github.com/echen/ggplot2-tutorial), which also contains some of the [example datasets](https://github.com/echen/ggplot2-tutorial/tree/master/data) I use and a [literate programming version](https://github.com/echen/ggplot2-tutorial/blob/master/ggplot2-tutorial.R) of this tutorial.





# Introduction





This is a bare-bones introduction to [ggplot2](http://had.co.nz/ggplot2/), a visualization package in [R](http://www.r-project.org/). It assumes no knowledge of R and teaches the minimum you'll need to know.





# Preview





Let's start with a preview of what ggplot2 can do.





Given Fisher's [iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) data set and one simple command...




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
      color = Species)
    </code>



  




...we can produce this plot of sepal length vs. petal length, colored by species.





[![Sepal vs. Petal, Colored by Species](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-specied.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-specied.png)





# Installation





You can download R [here](http://cran.opensourceresources.org/). After installation, you can launch R in interactive mode by either typing `R` on the command line or opening the standard GUI (which should have been included in the download).





# R Basics





## Vectors





Vectors are a core data structure in R, and are created with `c()`. Elements in a vector must be of the same type.




    
    <code>numbers = c(23, 13, 5, 7, 31)
    names = c("edwin", "alice", "bob")
    </code>



  




Elements are indexed starting at 1, and are accessed with `[]` notation.




    
    <code>numbers[1] # 23
    names[1] # edwin
    </code>



  




## Data frames





[Data frames](http://www.r-tutor.com/r-introduction/data-frame) are like matrices, but with named columns of different types (similar to [database tables](http://code.google.com/p/sqldf/)).




    
    <code>books = data.frame(
        title = c("harry potter", "war and peace", "lotr"),
        author = c("rowling", "tolstoy", "tolkien"),
        num_pages = c("350", "875", "500")
    )
    </code>



  




You can access columns of a data frame with `$`.




    
    <code>books$title # c("harry potter", "war and peace", "lotr")
    books$author[1] # "rowling"
    </code>



  




You can also create new columns with `$`.




    
    <code>books$num_bought_today = c(10, 5, 8)
    books$num_bought_yesterday = c(18, 13, 20)
    
    books$total_num_bought = 
          books$num_bought_today + books$num_bought_yesterday
    </code>



  




## read.table





Suppose you want to import a TSV file into R as a data frame.





### tsv file without header





For example, consider the [`data/students.tsv`](https://github.com/echen/r-tutorial/blob/master/data/students.tsv) file (with columns describing each student's age, test score, and name).




    
    <code>13   100 alice
    14   95  bob
    13   82  eve
    </code>



  




We can import this file into R using [`read.table()`](http://stat.ethz.ch/R-manual/R-devel/library/utils/html/read.table.html)




    
    <code>students = read.table(
        "data/students.tsv", 
        header = F, 
        sep = "\t", 
        col.names = c("age", "score", "name")
    )
    </code>



  




where







  * `header = F` means that the file does not contain a header (`F` is shorthand for `FALSE`)


  * `sep = "\t"` means that the file is tab-delimited


  * `col.names = c("age", "score", "name")` tells R the column names






We can now access the different columns in the data frame with `students$age`, `students$score`, and `students$name`.





### csv file with header





For an example of a file in a different format, look at the [`data/studentsWithHeader.tsv`](https://github.com/echen/r-tutorial/blob/master/data/studentsWithHeader.tsv) file.




    
    <code>age,score,name
    13,100,alice
    14,95,bob
    13,82,eve
    </code>



  




Here we have the same data, but now the file is comma-delimited and contains a header. We can import this file with




    
    <code>students = read.table("data/students.tsv", 
      header = T, sep = ",")
    </code>



  




By setting `header = T`, we tell R that the first line of the file contains column names, so we can immediately access `students$age` and so on. (Note: there is also a `read.csv` function that uses `sep = ","` by default.)





## help





There are many more options that `read.table` can take. For a full list of these, just type `help(read.table)` (or equivalently, `?read.table`) at the prompt to access documentation.





This works for other functions as well.





# ggplot2





With these R basics in place, let's dive into the ggplot2 package.





## Installation





One of R's greatest strengths is its excellent set of [packages](http://cran.r-project.org/web/packages/available_packages_by_name.html). To install a package, you can use the `install.packages()` function.




    
    <code>install.packages("ggplot2")
    </code>



  




To load a package into your current R session, use `library()`.




    
    <code>library(ggplot2)
    </code>



  




## Scatterplots with qplot()





Let's look at how to create a scatterplot in ggplot2. We'll use the `iris` data frame that's automatically loaded into R.





What does the data frame contain? We can use the `head` function to look at the first few rows.




    
    <code>head(iris) # by default, head displays the first 6 rows
    head(iris, n = 10) # explicitly set number of rows
    
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
               5.1         3.5          1.4         0.2  setosa
               4.9         3.0          1.4         0.2  setosa
               4.7         3.2          1.3         0.2  setosa
               4.6         3.1          1.5         0.2  setosa
               5.0         3.6          1.4         0.2  setosa
               5.4         3.9          1.7         0.4  setosa
    </code>



  




(The data frame actually contains three types of species: setosa, versicolor, and virginica.)





Let's plot `Sepal.Length` against `Petal.Length` using ggplot2's `qplot()` function.




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris)
    # Plot Sepal.Length vs. Petal.Length, 
    # using data from the `iris` data frame.
    </code>



  




[![Sepal Length vs. Petal Length](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal.png)





To see where each species is located in this graph, we can color each point by adding a `color = Species` argument.




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
      color = Species) # dude!
    </code>



  




[![Sepal vs. Petal, Colored by Species](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-specied.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-specied.png)





Similarly, we can let the size of each point denote sepal width, by adding a `size = Sepal.Width` argument.




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
      color = Species, size = Petal.Width)
    # We see that Iris setosa flowers have the narrowest petals.
    </code>



  




[![Sepal vs. Petal, Sized by Petal Width](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-sized.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-sized.png)




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
      color = Species, size = Petal.Width, alpha = I(0.7))
    # By setting the alpha of each point to 0.7, 
    # we reduce the effects of overplotting.
    </code>



  




[![Sepal vs. Petal, with Transparency](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-alpha.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-alpha.png)





Finally, let's fix the axis labels and add a title to the plot.




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
        color = Species,
        xlab = "Sepal Length", ylab = "Petal Length", 
        main = "Sepal vs. Petal Length in Fisher's Iris data")
    </code>



  




[![Sepal vs. Petal, Titled](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-titled.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-titled.png)





## Other common geoms





In the scatterplot examples above, we implicitly used a _point_ **geom**, the default when you supply two arguments to `qplot()`.




    
    <code># These two invocations are equivalent.
    qplot(Sepal.Length, Petal.Length, data = iris, 
      geom = "point")
    qplot(Sepal.Length, Petal.Length, data = iris)
    </code>



  




But we can also easily use other types of geoms to create more kinds of plots.





### Barcharts: geom = "bar"




    
    <code>movies = data.frame(
        director = c("spielberg", "spielberg", "spielberg", 
            "jackson", "jackson"),
        movie = c("jaws", "avatar", "schindler's list", "lotr", 
            "king kong"),
        minutes = c(124, 163, 195, 600, 187)
    )
    
    # Plot the number of movies each director has.
    qplot(director, data = movies, geom = "bar", 
      ylab = "# movies")
    # By default, the height of each bar is simply a count.
    </code>



  




[![# Movies](http://dl.dropbox.com/u/10506/blog/r/ggplot2/num-movies.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/num-movies.png)




    
    <code># But we can also supply a different weight.
    # Here the height of each bar is 
    # the total running time of the director's movies.
    qplot(director, weight = minutes, data = movies, 
      geom = "bar", ylab = "total length (min.)")
    </code>



  




[![Total Running Time](http://dl.dropbox.com/u/10506/blog/r/ggplot2/total-length.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/total-length.png)





### Line charts: geom = "line"




    
    <code>qplot(Sepal.Length, Petal.Length, data = iris, 
      geom = "line", color = Species) 
    # Using a line geom doesn't really make sense here, but hey.
    </code>



  




[![Sepal vs. Petal, Lined](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-lined.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/sepal-vs-petal-lined.png)




    
    <code># `Orange` is another built-in data frame 
    # that describes the growth of orange trees.
    qplot(age, circumference, data = Orange, geom = "line",
        colour = Tree,
        main = "How does orange tree circumference 
          vary with age?")
    </code>



  




[![Orange Tree Growth](http://dl.dropbox.com/u/10506/blog/r/ggplot2/orange-tree-growth.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/orange-tree-growth.png)




    
    <code># We can also plot both points and lines.
    qplot(age, circumference, data = Orange, 
      geom = c("point", "line"), colour = Tree)
    </code>



  




[![Orange Tree with Points](http://dl.dropbox.com/u/10506/blog/r/ggplot2/orange-tree-pointed.png)](http://dl.dropbox.com/u/10506/blog/r/ggplot2/orange-tree-pointed.png)





# Next Steps





In this post, I skipped over a lot of aspects of R and ggplot2.





For example,







  * There are many geoms (and other functionalities) in ggplot2 that I didn't cover, e.g., [boxplots](http://had.co.nz/ggplot2/geom_boxplot.html) and [histograms](http://had.co.nz/ggplot2/geom_histogram.html).


  * I didn't talk about ggplot2's layering system, or the [grammar of graphics](http://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448) it's based on.






So I'll end with some additional resources on R and ggplot2.







  * I don't use it myself, but [RStudio](http://rstudio.org/) is a popular IDE for R.


  * The [official ggplot2 documentation](http://had.co.nz/ggplot2/) is great and has lots of examples. There's also an excellent [book](http://www.amazon.com/ggplot2-Elegant-Graphics-Data-Analysis/dp/0387981403).


  * [plyr](http://plyr.had.co.nz/) is another fantastic R package that's also by Hadley Wickham (the author of ggplot2).


  * The [official R introduction](http://cran.r-project.org/doc/manuals/R-intro.html) is okay, but definitely not great. I haven't found any R tutorials I really like, but I've heard good things about [The Art of R Programming](http://www.amazon.com/Art-Programming-Statistical-Software-Design/dp/1593273843).


