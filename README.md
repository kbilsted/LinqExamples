# 1. LinqExamples

we show 2 different ways of implementing an average of 3 elements of a lazy stream. Method 1 is the least general and reusable.

# 2. Code
    using System;
    using System.Collections.Generic;
    using System.Linq;

    namespace LinqJenkovAverage
    {
        class Program
        {
            static void Main(string[] args)
            {
                var numbers = new[] { 3, 4, 5, 6, 7, 8, 9, 1, 2, 3 };

                Example1(numbers);
             
                Console.WriteLine("----------");
                
                Example2(numbers);
            }

            private static void Example1(int[] numbers)
            {
                var jenkovAvg = numbers.Select(x => x + 1).JenkovAverage().GetEnumerator();

                jenkovAvg.MoveNext();
                Console.WriteLine(jenkovAvg.Current);

                jenkovAvg.MoveNext();
                Console.WriteLine(jenkovAvg.Current);
            }

            private static void Example2(int[] numbers)
            {
                var jenkovAvg = numbers.Select(x => x + 1).Chunk(3).Select(x=>x.Average()).GetEnumerator();

                jenkovAvg.MoveNext();
                Console.WriteLine(jenkovAvg.Current);

                jenkovAvg.MoveNext();
                Console.WriteLine(jenkovAvg.Current);
            }

        }

        public static class JenkovLinq
        {
            public static IEnumerable<int[]> Chunk(this IEnumerable<int> input, int size)
            {
                var enume = input.GetEnumerator();
                while (true)
                {
                    var result = new int[size];
                    for (int i = 0; i < size; i++)
                    {
                        if (!enume.MoveNext())
                            yield break;

                        result[i] = enume.Current;
                    }

                    yield return result;
                }
            }

            public static IEnumerable<int> JenkovAverage(this IEnumerable<int> input)
            {
                using (var enume = input.GetEnumerator())
                {
                    while (enume.MoveNext())
                    {
                        int a = enume.Current;
                        enume.MoveNext();
                        int b = enume.Current;
                        enume.MoveNext();
                        int c = enume.Current;

                        yield return (a + b + c) / 3;
                    }
                };
            }
        }

    }


# 3. execution

	5
	8
	----------
	5
	8
