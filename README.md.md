# WordFinderChallenge2024
A high-performance word search in a character matrix

using System;
using System.Collections.Generic;
using System.Linq;

public class WordFinder
{
    private readonly char[,] _matrix;
    private readonly int _rows;
    private readonly int _cols;

    public WordFinder(IEnumerable<string> matrix)
    {
        var matrixList = matrix.ToList();
        _rows = matrixList.Count;
        _cols = matrixList[0].Length;

        _matrix = new char[_rows, _cols];
        for (int i = 0; i < _rows; i++)
        {
            for (int j = 0; j < _cols; j++)
            {
                _matrix[i, j] = matrixList[i][j];
            }
        }
    }

    public IEnumerable<string> Find(IEnumerable<string> wordStream)
    {
        var wordSet = new HashSet<string>(wordStream); // Ensure no duplicates in the input stream.
        var wordCounts = new Dictionary<string, int>();
        
        foreach (var word in wordSet)
        {
            if (IsWordInMatrix(word))
            {
                wordCounts[word] = wordCounts.ContainsKey(word) ? wordCounts[word] + 1 : 1;
            }
        }

        // Return the top 10 most frequent words or fewer.
        return wordCounts
            .OrderByDescending(kvp => kvp.Value)
            .Take(10)
            .Select(kvp => kvp.Key);
    }

    private bool IsWordInMatrix(string word)
    {
        // Check for the word in horizontal and vertical directions.
        return FindHorizontal(word) || FindVertical(word);
    }

    private bool FindHorizontal(string word)
    {
        for (int i = 0; i < _rows; i++)
        {
            for (int j = 0; j <= _cols - word.Length; j++)
            {
                bool match = true;
                for (int k = 0; k < word.Length; k++)
                {
                    if (_matrix[i, j + k] != word[k])
                    {
                        match = false;
                        break;
                    }
                }
                if (match) return true;
            }
        }
        return false;
    }

    private bool FindVertical(string word)
    {
        for (int i = 0; i < _cols; i++)
        {
            for (int j = 0; j <= _rows - word.Length; j++)
            {
                bool match = true;
                for (int k = 0; k < word.Length; k++)
                {
                    if (_matrix[j + k, i] != word[k])
                    {
                        match = false;
                        break;
                    }
                }
                if (match) return true;
            }
        }
        return false;
    }
}

// Usage Example
class Program
{
    static void Main()
    {
        var matrix = new List<string>
        {
            "chill",
            "abcde",
            "fghij",
            "klmno",
            "pqrst"
        };

        var wordStream = new List<string> { "chill", "cold", "wind", "snow" };

        var wordFinder = new WordFinder(matrix);
        var result = wordFinder.Find(wordStream);

        Console.WriteLine("Top words found:");
        foreach (var word in result)
        {
            Console.WriteLine(word);
        }
    }
}
