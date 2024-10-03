#  안드로이드 스튜디오에서 HTTP 요청 보내기

안드로이드 앱에서 Flask 서버에 POST 요청을 보내야 함. \
Retrofit 라이브러리를 사용할 수 있음.   Retrofit은 REST API와의 통신을 쉽게 해주는 라이브러리

##  Retrofit 설정
### 1. build.gradle (Module: app) 파일에 Retrofit 라이브러리 추가:
```
dependencies {
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.9.0'
}
```

### 2. Retrofit API 인터페이스 생성:
```
   public interface ApiService {
    @POST("/receive_text")
    Call<PlaylistResponse> sendText(@Body TextRequest textRequest);
}
```

### 3. TextRequest와 PlaylistResponse 클래스 생성:
```
public class TextRequest {
    private String text;

    public TextRequest(String text) {
        this.text = text;
    }
}

public class PlaylistResponse {
    private String playlist_url;

    public String getPlaylistUrl() {
        return playlist_url;
    }
}
```


### 4. Retrofit 인스턴스 생성:
```
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://your-ec2-public-ip:5000") // EC2의 퍼블릭 IP와 포트
        .addConverterFactory(GsonConverterFactory.create())
        .build();

ApiService apiService = retrofit.create(ApiService.class);
```
--> 여기서 .baseUrl("http://your-ec2-public-ip:5000") // EC2의 퍼블릭 IP와 포트 이 부분에\
http://3.26.61.213:5000 
    <---- 이 주소삽입

### 5. Flask 서버에 POST 요청 보내기:
```
TextRequest textRequest = new TextRequest("당신이 입력한 텍스트");
Call<PlaylistResponse> call = apiService.sendText(textRequest);

call.enqueue(new Callback<PlaylistResponse>() {
    @Override
    public void onResponse(Call<PlaylistResponse> call, Response<PlaylistResponse> response) {
        if (response.isSuccessful()) {
            String playlistUrl = response.body().getPlaylistUrl();
            // 플레이리스트 URL을 사용할 수 있습니다.
        } else {
            // 오류 처리
        }
    }

    @Override
    public void onFailure(Call<PlaylistResponse> call, Throwable t) {
        // 네트워크 오류 처리
    }
});
```


# 통신흐름요약

1. 안드로이드 앱에서 사용자가 텍스트를 입력하면, 입력된 텍스트를 포함한 POST 요청을 Flask 서버로 보냄
2. Flask 서버는 요청을 받아 텍스트를 처리한 후 플레이리스트 URL을 반환
3. 안드로이드 앱은 서버로부터 받은 플레이리스트 URL을 사용

