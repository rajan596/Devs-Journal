
```java
package com.adobe.java;

import java.util.HashMap;
import java.util.Map;
import java.util.Random;

/**
 * Characters in Short URL: [a-z][A-Z][0-9] We have total 62 characters.
 * Taking 6 characters will generate 62^6 unique combinations which will go beyond 1B urls
 * */
public class URLShortner {
    Random random = new Random();
    private static final Long MAX_URLS = 1000000000L;
    private static final int TOTAL_CHARS = 62;
    private static final int SHORT_URL_LEN = 6;
    Map<String, String> shortUrlDB = new HashMap<>(); // short url -> original url mapping

    public String createShortURL(String url){
        long randomNumber = random.nextLong() % MAX_URLS;
        if(randomNumber < 0) randomNumber = -randomNumber;
        String shortURL = "https://bit.ly/" + encodeNumber(randomNumber);
        shortUrlDB.put(shortURL, url);
        return shortURL;
    }

    private String encodeNumber(Long no) {
        StringBuilder sb = new StringBuilder();
        for(int i=0;i<SHORT_URL_LEN;i++){
            int remainder = (int) (no % TOTAL_CHARS);
            no = no / TOTAL_CHARS;
            sb.append(toChar(remainder));
        }
        return sb.toString();
    }

    private char toChar(int remainder) {
        if(remainder >= 0 && remainder <= 9) return (char) ('0'+remainder);
        else if(remainder >= 10 && remainder <= 35) return (char) ('a'+remainder-10);
        else return (char) ('A'+remainder-10-26);
    }

    public String getOriginalURLFromShortURL(String shortURL){
        return shortUrlDB.get(shortURL);
    }

    public static void main(String[] args) {
        URLShortner urlShortner = new URLShortner();
        String shortURL = urlShortner.createShortURL("https://leetcode.com/problemset/?search=934&page=1");
        String originalURL = urlShortner.getOriginalURLFromShortURL(shortURL);
        System.out.println("Original URL: " +  originalURL + " -> " + shortURL);
    }
}

```