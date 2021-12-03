 # JSON

<pre>
<code>
// name-value 형식
// { String key : String Value}

{
    "landUses": {
        "field": [
            {
                "regstrSeCode": "1",
                "pnu": "5011013500107450000",
                "regstrSeCodeNm": "토지대장",
                "ldCode": "5011013500",
                "ldCodeNm": "제주특별자치도 제주시 월평동",
                "mnnmSlno": "745",
                "lastUpdtDt": "2021-11-19",
                "manageNo": "14110005011020110003UHJ4000001001",
                "cnflcAt": "3",
                "cnflcAtNm": "접함",
                "prposAreaDstrcCode": "UHJ400",
                "prposAreaDstrcCodeNm": "녹지 구역",
                "registDt": "2017-11-06"
            }
        ],
        "totalCount": "14",
        "numOfRows": "1",
        "pageNo": "1",
        "resultCode": null,
        "resultMsg": null
    }
}
</pre>
</code>


<pre>
<code>
static String DATA_KEY = "인증키";

@ResponseBody
@PostMapping("/api/name")
public Map<String, Object> name() throws Exception {
	Map<String, Object> resultMap = new HashMap<String, Object>();

	StringBuilder urlBuilder = new StringBuilder("공공데이터 URL");
	/* Service Key */
	urlBuilder.append("?" + URLEncoder.encode("ServiceKey", "UTF-8") + "=" + DATA_KEY);
	/* 응답결과 형식(xml 또는 json) */
	urlBuilder.append("&" + URLEncoder.encode("format", "UTF-8") + "=" + URLEncoder.encode("json", "UTF-8"));
	/* 출력 건수 */
	urlBuilder.append("&" + URLEncoder.encode("numOfRows", "UTF-8") + "=" + URLEncoder.encode("10", "UTF-8"));
	/* 페이지 번호*/
	urlBuilder.append("&" + URLEncoder.encode("pageNo", "UTF-8") + "=" +URLEncoder.encode("1", "UTF-8"));

	String line = getLine(urlBuilder.toString());

	System.out.println("URL :: "+urlBuilder.toString());

	if (line != null && !line.isEmpty()) {
		@SuppressWarnings("unchecked")
		Map<String, Object> data = new ObjectMapper().readValue(line, Map.class);

		System.out.println("data :: "+data);
		if (!(data == null || data.isEmpty())) {
			@SuppressWarnings("unchecked")
			Map<String, Object> response = (Map<String, Object>) data.get("KEY"); // JSON 결과값의 첫 key 
			@SuppressWarnings("unchecked")
			List<Map<String, Object>> field = (List<Map<String, Object>>) response.get("field");
			String totalCount = (String) response.get("totalCount");

			resultMap.put("resultList", field);
			resultMap.put("resultTotal", totalCount);
		}
	}

	return resultMap;
}

public String getLine(String callUrl) throws Exception {
	BufferedReader br = null;
	String line = "";
	URL obj = new URL(callUrl); // 호출할 url
	HttpURLConnection con = (HttpURLConnection) obj.openConnection();
	con.setRequestMethod("GET");
	br = new BufferedReader(new InputStreamReader(con.getInputStream(), "UTF-8"));
	line = br.readLine(); // API 호출 응답값
	return line;
}

</code>
</pre>

 # XML


```xml
<response>
    <header>
        <resultCode>00</resultCode>
        <resultMsg>NORMAL SERVICE.</resultMsg>
    </header>
    <body>
        <items>
            <item>
                <cnt>203</cnt>
                <distance>0.16</distance>
                <dutyAddr>제주특별자치도 제주시 신성로 78 (도남동)</dutyAddr>
                <dutyDiv>H</dutyDiv>
                <dutyDivName>약국</dutyDivName>
                <dutyName>한결약국</dutyName>
                <dutyTel1>064-727-1210</dutyTel1>
                <endTime>1900</endTime>
                <hpid>C2900139</hpid>
                <latitude>33.49543487255906</latitude>
                <longitude>126.52780190425668</longitude>
                <rnum>112</rnum>
                <startTime>0830</startTime>
            </item>
        </items>
        <numOfRows>1</numOfRows>
        <pageNo>1</pageNo>
        <totalCount>199</totalCount>
    </body>
</response>
```
<pre>
<code>
@SuppressWarnings("unchecked")
@ResponseBody
@PostMapping("/api/name")
public Map<String, Object> name() throws Exception{
	Map<String, Object> resultMap = new HashMap<>();

	StringBuilder urlBuilder = new StringBuilder("URL");
	/* Service Key */
	urlBuilder.append("?" + URLEncoder.encode("ServiceKey", "UTF-8") + "=" + DATA_KEY);
	/* 출력 갯수*/
	urlBuilder.append("&" + URLEncoder.encode("numOfRows", "UTF-8") + "=" + URLEncoder.encode("10", "UTF-8"));
	/* 페이지번호*/
	urlBuilder.append("&" + URLEncoder.encode("pageNo", "UTF-8") + "=" + URLEncoder.encode("1", "UTF-8"));
	
	URL url = new URL(urlBuilder.toString());

	HttpURLConnection conn = (HttpURLConnection) url.openConnection();
	conn.setRequestMethod("GET");
	conn.setRequestProperty("Content-type", "application/json");
	BufferedReader rd;
	if (conn.getResponseCode() >= 200 && conn.getResponseCode() <= 300) {
		rd = new BufferedReader(new InputStreamReader(conn.getInputStream()));
	} else {
		rd = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
	}
	StringBuilder sb = new StringBuilder();
	String line;
	while ((line = rd.readLine()) != null) {
		sb.append(line);
	}
	rd.close();
	conn.disconnect();

	JSONObject xmlJSONObj = XML.toJSONObject(sb.toString());
	String xmlJSONObjString = xmlJSONObj.toString();

	Map<String, Object> data = new ObjectMapper().readValue(xmlJSONObjString, Map.class);
	if (!(data == null || data.isEmpty())) {
		Map<String, Object> response = (Map<String, Object>) data.get("response");
		Map<String, Object> body = (Map<String, Object>) response.get("body");
		Map<String, Object> items = (Map<String, Object>) body.get("items");

		// 결과값이 여러개일경우
		List<Map<String, Object>> item = (List<Map<String, Object>>) items.get("item");
		// 결과값이 하나인 경우
		Map<String, Object> item = (Map<String, Object>) body.get("item");
		
		resultMap.put("result", item);
	}
	
	return resultMap;
}

</code>
</pre>