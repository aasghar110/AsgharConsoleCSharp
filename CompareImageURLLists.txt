using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;

namespace FaceComparisonConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            try
            {
                string apiUrl = "https://api-us.faceplusplus.com/facepp/v3/compare";
                string apiKey = "4LkcmmlGb7IoRoE3FCsmbromtK5uzcao";
                string apiSecret = "ebmmqsVi0GmLOQO64ZDbjghYHa3bJh_I";
                double confidenceThreshold = 80.0; // Set confidence threshold

                // List of missing and found report image URLs
                List<string> MissingReportList = new List<string>
                {
                    "https://gentececom.com/asgharwebapi/uploads/boy1.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy2.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy4.jpg"
                };

                List<string> FoundReportList = new List<string>
                {
                    "https://gentececom.com/asgharwebapi/uploads/girl1.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl2.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/girl3.jpg",
                    "https://gentececom.com/asgharwebapi/uploads/boy4.jpg"
                };

                // Create HttpClient
                using (HttpClient client = new HttpClient())
                {
                    foreach (string missingUrl in MissingReportList)
                    {
                        foreach (string foundUrl in FoundReportList)
                        {
                            var parameters = new Dictionary<string, string>
                            {
                                { "api_key", apiKey },
                                { "api_secret", apiSecret },
                                { "image_url1", missingUrl },
                                { "image_url2", foundUrl }
                            };

                            HttpResponseMessage response = await client.PostAsync(apiUrl, new FormUrlEncodedContent(parameters));

                            if (response.IsSuccessStatusCode)
                            {
                                var result = await response.Content.ReadAsAsync<FacePlusPlusResponse>();
                                double confidence = result.Confidence;

                                if (confidence > confidenceThreshold)
                                {
                                    Console.WriteLine($"Confidence of image URLs {missingUrl} and {foundUrl} is {confidence} (above 80)");
                                }
                            }
                            else
                            {
                                Console.WriteLine($"Face++ API request failed for {missingUrl} and {foundUrl}: {response.ReasonPhrase}");
                            }

                            // Introduce a delay of 0.5 seconds
                            await Task.Delay(400);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("An error occurred: " + ex.Message);
            }

            Console.ReadLine();
        }
    }

    public class FacePlusPlusResponse
    {
        public double Confidence { get; set; }
    }
}
