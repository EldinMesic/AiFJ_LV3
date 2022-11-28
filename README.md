using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;

namespace LV3_2
{
    internal class Program
    {

        static string type = String.Empty;
        static List<string> IdentificatorList = new List<string>();
        static List<string> ConstantList = new List<string>();
        static List<string> CommentsList = new List<string>();
        static string[] lines;
        static void Main(string[] args)
        {

            string path = @"D:\TestCode\";
            string fileName = Console.ReadLine();
            lines = File.ReadAllLines(path + fileName);



            int lineNumber = 0;
            foreach (string line in lines)
            {
                lineNumber++;
                string result = $"Line {lineNumber}: {line}";
                string[] words = line.Split(' ');
                if (line != String.Empty)
                {
                    foreach (string word in words)
                    {
                        string newString = CheckSyntax(word);
                        if (newString != String.Empty)
                        {
                            newString = newString.Substring(0, newString.Length - 17);
                            result += newString;
                        }

                    }
                    
                }
                result += "\n";
                Console.WriteLine(result);
            }



            string countedResult = "";

            List<string> KeywordList = new List<string>();
            KeywordList.Add("number");
            KeywordList.Add("word");
            KeywordList.Add("major");
            KeywordList.Add("vrati");

            List<string> SeperatorList = new List<string>();
            SeperatorList.Add("/");
            SeperatorList.Add("\\");
            SeperatorList.Add("'");
            SeperatorList.Add("(");
            SeperatorList.Add(")");
            SeperatorList.Add(".");

            List<string> OperatorList = new List<string>();
            OperatorList.Add("-");
            OperatorList.Add("+");
            OperatorList.Add("=");
            OperatorList.Add("*");


            CommentsList.Add("<");
            CommentsList.Add(">");




            countedResult += CountCode("Identifikatori", IdentificatorList);
            countedResult += CountCode("Ključne riječi", KeywordList);
            countedResult += CountCode("Seperatori", SeperatorList);
            countedResult += CountCode("Operatori", OperatorList);
            countedResult += CountCode("Konstante", ConstantList);
            countedResult += CountCode("Komentari", CommentsList);


            Console.WriteLine(countedResult);


        }


        private static string CheckSyntax(string word)
        {
            string result = "";


            if (word == String.Empty)
            {
                return "";
            }

            if (type == String.Empty)
            {

                if (word == "number" || word == "word" || word == "vrati" || word == "major")
                {
                    result += $"\n('{word}', kljucna rijec)";
                }          

                else if (word.StartsWith("<"))

                {
                    result += $"\n('{word[0]}', komentar)";

                    type = "komentar";

                    if (word.Length > 1)
                    {
                        result += CheckSyntax(word.Substring(1, word.Length-1));
                        result = result.Substring(0, result.Length - 17);                        
                    }
         
                }
                else if (word.StartsWith("'"))

                {
                    result += $"\n('{word[0]}', seperator)";

                    type = "konstanta";

                    if (word.Length > 1)
                    {
                        result += CheckSyntax(word.Substring(1, word.Length - 1));
                        result = result.Substring(0, result.Length - 17);
                    }

                }

                else if (word.Contains("(") || word.Contains("\'") || word.Contains("\\"))
                {
                    result += $"\n('{word}', seperator)";
                }
                else if (word == "+" || word == "-" || word == "=" || word == "*")
                {
                    result += $"\n('{word}', operator)";
                }
                else if (word.EndsWith("."))
                {
                    if (word.Length > 1)
                    {
                        result += CheckSyntax(word.Substring(0, word.Length-1));
                        result = result.Substring(0, result.Length - 17); 
                    }
                    result += $"\n('{word[word.Length-1]}', operator)";
                }
                else if (word.EndsWith("/") || word.EndsWith(")"))
                {
                    if (word.Length > 1)
                    {
                        result += CheckSyntax(word.Substring(0, word.Length - 1));
                        result = result.Substring(0, result.Length - 17);
                    }
                    result += $"\n('{word[word.Length - 1]}', seperator)";
                }
                else if (word.Length <= 3)
                {
                    result += $"\n('{word}', identifikator)";
                    if (!IdentificatorList.Contains(word))
                        IdentificatorList.Add(word);
                }
            }
            else
            {
                if (word.EndsWith(">") || word.EndsWith("'"))
                {
                    if(word.Length > 1)
                    {
                        result += CheckSyntax(word.Substring(0, word.Length - 1));
                        result = result.Substring(0, result.Length - 17);
                    }

                    if(word.EndsWith(">"))
                        result += $"\n('{word[word.Length - 1]}', {type})";
                    else
                        result += $"\n('{word[word.Length - 1]}', seperator)";
                    type = String.Empty;
                }
                else
                {
                    result += $"\n('{word}', {type})";

                    if (type == "komentar")
                    {
                        if (!CommentsList.Contains(word))
                            CommentsList.Add(word);
                    }
                    else if(type == "konstanta")
                    {
                        if (!ConstantList.Contains(word))
                            ConstantList.Add(word);
                    }
                    
                }
                    
            }

            result += "\n(' ', seperator)";
            return result;
        }

        static string CountCode(string type, List<string> list)
        {
            int totalCount = 0;
            string returnString = "";


            foreach(string item in list)
            {
                int itemCount = 0;

                foreach (string line in lines)
                {
                    string[] words = line.Split(' ');

                    foreach(string word in words)
                    {
                        if (word.Contains(item))
                        {
                            itemCount++;
                            totalCount++;
                        }
                            
                    }
                }

                returnString += $" '{item}'[{itemCount}]";

            }

            if (totalCount == 0)
            {
                return $"{type} [{totalCount}]" + "\n";
            }

            returnString = $"{type} [{totalCount}]:" + returnString + "\n";

            return returnString;
        }
    }
}
