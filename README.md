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
                var numbers = new[] { 1, 2, 3, 4, 5, 6, 7 };

                Example1(numbers);
                Console.WriteLine("----------");
                Example2(numbers);
            }

            private static void Example1(int[] numbers)
            {
                var jenkovAvg = numbers.Select(x => x + 2).JenkovAverage().GetEnumerator();

                Console.WriteLine(jenkovAvg.Current);
                jenkovAvg.MoveNext();

                Console.WriteLine(jenkovAvg.Current);
                jenkovAvg.MoveNext();
            }

            private static void Example2(int[] numbers)
            {
                var jenkovAvg = numbers.Select(x => x + 2).Chunk(3).Select(x=>x.Average()).GetEnumerator();

                Console.WriteLine(jenkovAvg.Current);
                jenkovAvg.MoveNext();

                Console.WriteLine(jenkovAvg.Current);
                jenkovAvg.MoveNext();
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
                        result[i] = enume.Current;

                        if(!enume.MoveNext())
                            yield break;
                    }

                    yield return result;
                }
            }

            public static IEnumerable<int> JenkovAverage(this IEnumerable<int> input)
            {
                var enume = input.GetEnumerator();
                int a = enume.Current;
                enume.MoveNext();
                int b = enume.Current;
                enume.MoveNext();
                int c = enume.Current;
                enume.MoveNext();

                yield return (a + b + c) / 3;
            }
        }

    }



# 3. execution

	0
	2
	----------
	0
	2,3333333333333335

the difference in results is the second example converts the calculation to decimal
