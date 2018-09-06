import java.util.regex.Matcher
import java.util.regex.Pattern
import javax.xml.xpath.*

// set target url to artifact versions in you maven repo
URL targetUrl = new URL("https://mvn.havre.de/repo/de/havre/" + artifactId + "/");

// resolve HTML version list
HttpURLConnection con = (HttpURLConnection) targetUrl.openConnection();
con.setRequestMethod("GET");
con.setRequestProperty("Authorization", "Basic ***********************");
int responseCode = con.getResponseCode();

// pars HTML to a simple list
if (responseCode == HttpURLConnection.HTTP_OK) {
               BufferedReader inStream = new BufferedReader(new InputStreamReader(con.getInputStream()));
               String inputLine;
               StringBuffer response = new StringBuffer();
               while ((inputLine = inStream.readLine()) != null) {
                              response.append(inputLine);
               }
               inStream.close();

               Pattern TAG_REGEX = Pattern.compile("<text>([0-9]*\\.[0-9]*\\.[0-9]*)</text>");
               List tagValues = new ArrayList();
               final Matcher matcher = TAG_REGEX.matcher(response.toString());
               while (matcher.find()) {
                              tagValues.add(matcher.group(1));
               }
        Collections.reverse(tagValues);
        return tagValues;
} else {
               return['GET ' + targetUrl + ' not worked: ' + responseCode ];
}
