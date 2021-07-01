# Jurusan.js
Jurusan.js is a JavaScript autocomplete library for BINUS University list of study programs (majors). This library does not require external dependencies and can be used for web browsers and Node.js.

## How Does It Work?
### The Data Structure
The entire database of available study programs is contained in a **Directed Acyclic Graph (DAG)**, where one node (which can represent a major, school, faculty, etc.) may be related to other nodes as long as they do not form any loop.

There is a relationship hierarchy between the programs and respective schools/faculties, which can be found on the node's `type` property:

1. **Program Level**, which could be either Undergraduate (S1 & D3), Master (S2), and Doctoral (S3)
2. **Faculty**, which represent a Faculty, School or asuch as School of Computer Science (SOCS)
3. **Subfaculty**, currently only used to distinguish several programs under **Faculty of Economics and Communication** as well as the **BINUS Business School**
4. **Program Family**, which represent a group which consists of slight variations under the same program name, such as **Visual Communication Design (VCD/DKV)** which may be further divided into **DKV Animation**, **DKV Creative Advertising**, and **DKV New Media**
5. **Program**, which represents a particular study program
6. **Stream**, which indicates one of available streams of a study program

Each node may define a relationship through `inherits`, `children`, `didYouMean`, and `seeAlso`, which are explained below:

+ `inherits` attributes indicates a "part of" relation between another node. For example, the Computer Science family of programs is part of the School of Computer Science, hence "Computer Science" inherits all the "School of Computer Science" node and attributes.
+ `children` attributes are direct opposites of `inherits`, generated at compile-time.
+ `didYouMean` attributes indicates direct aliases for a particular node.
+ `seeAlso` attributes indicates any related nodes (e.g. programs or program families) which bears a similar name to the node which do not qualify as a child and an alias.

The database as found on the source code will be (re)compiled to include aliases according to their initials (e.g. "Mobile Application and Technology" as "MAT") as well as the preceeding words (e.g. "Mobile Application and Technology" to "Mobile Application and", "Mobile Application", and simply "Mobile"). This enables the search algorithm to suggest study programs from the programs' first words (or initials), which is essential for the autocorrect feature.

### Searching Strategy
We are using 2 different strategies for searching:

1. Find the exact match, then/or
2. Find the closest match by either their initials (for one-word search) or first *N* words.

Once the result has been found, the algorithm always try to find other as well as validate possible matches until the limits has been reached. The results will be sorted by their **confidence levels**, which is our cool term to indicate how many times that the match result occurs during the processing.

### Recursion
And of course, this library uses recursion, and we really love it! To learn more about recursion, visit [Recursion](https://github.com/himtibinus/jurusan.js#recursion).

## Usage
Simply create a new `Jurusan` instance and call the `search()` function, to get an array of suggested study program names.

```js
var jurusan = new Jurusan();
jurusan.search("GAT"); // Will return "Game Application and Technology" inside an array
```

**Please note that you do not need to create more than one `Jurusan` instance on your project.** Creating more than one instance means recompiling the study program database, which may affect your project's overall performance. The same instance can be reused to search for study program names on different functions, even when they are async.

## Browser Support
The entire library is written using JavaScript classes (as in Object-Oriented Programming, not HTML/CSS DOM classes), hence no support for Internet Explorer. A detailed list of supported browser versions is available on [caniuse](https://caniuse.com/mdn-javascript_classes_constructor).
