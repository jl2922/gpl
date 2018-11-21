# FGPL
Fast Generic Parallel Library

[![Build Status](https://travis-ci.org/jl2922/fgpl.svg?branch=master)](https://travis-ci.org/jl2922/fgpl)

## Usage
TBA

## Example
Count number of occurences of each word:
```c++
std::vector<std::string> lines;
// load data ...
fgpl::DistRange<int> range(0, lines.size());
fgpl::DistHashMap<std::string, int> target;
const auto& mapper = [&](const int i, const auto& emit) {
  std::stringstream ss(lines[i]);
  std::string word;
  while (std::getline(ss, word, ' ')) {
    emit(word, 1);
  }
};
range.mapreduce<std::string, int, std::hash<std::string>>(
    mapper, fgpl::Reducer<int>::sum, target);
std::cout << target.get_n_keys() << std::endl;  // Output number of distinct words.
```

## Benchmark
![Serialize and Parse Time](https://github.com/jl2922/fgpl/blob/master/performance.png)

Spark's implementations are from https://spark.apache.org/examples.html.
The Spark cluster is set up using AWS EMR's default settings.

FGPL's implementations are in the DistHashMapTest and DistRangeTest of CI.

The input file for word count is from Shakespeare and the Bible, repeated ~100 times to make it ~1GB in size.
We use MapReduce to count the number of occurrences of each word.
