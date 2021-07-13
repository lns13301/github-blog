---
layout: post
title: 우아한테크코스 level 2-1 체스 회고 및 학습 프로필
date: 2021-06-20 19:15:00 +0900
description: jwp chess 학습 프로필, 회고
img: woowacourse.png
tags: spring study
---

# 와일더의 학습 프로필 🃏

매번 느끼지만 남들이 사용해서 따라 사용하는 것이 아닌, 내가 코드를 작성 해보고 리뷰를 받으면서 필요에 의해서 새로운 내용들을 적용시키는 학습방법은 매우 좋다고 느낍니다. 이유를 알고 적용을 하니 이해도 잘되고 금방 적용 해볼 수 있게 되는 것 같습니다. 😋

## [ORM] DAO and Repository - 5

### 내용

- <b>Repository</b>

  - 도메인 객체의 생명주기를 관리하며 Public Operation 을 제공한다.
  - Repository 에서 제공하는 하나의 오퍼레이션이 DAO 의 여러 오퍼레이션에 매핑되는 것이 일반적이다.
  - Domain Layer 에 해당한다.
    - 도메인 모델과 생애주기가 같기 때문이다.
  - 구체화에 의존하지 않고 추상화에 의존한다. [(DIP)](https://lns13301.github.io/github-blog/solid/)
    - add, find, delete 같은 기능만 추상화 해둔다. (맞나?)

- <b> DAO</b>

  - CRUD 와 1:1로 매칭되어 Persistence Operation 을 적극적으로 드러낸다.
  - Repository 는 도메인 모델의 일부로 보며 추상적이지만, DAO 는 추상화가 필수가 아니다.
  - DAO 의 인터페이스는 데이터베이스의 CRUD 쿼리와 1:1 매칭되는 세밀한 단위의 오퍼레이션을 제공한다. 

- 하나의 Repository 가 다수의 DAO 를 호출하는 방식으로 Repository 를 구현할 수 있다.

  - 그래서 Repository 를 추상화하고 DAO 에서 구현하는 방식으로 코드를 만들어 봤다.

    ```java
    public interface ChessRepository {
        Long add(MoveRequestDto moveRequestDto);
    
        List<CommandDto> find(String roomId);
    
        void delete(String roomId);
    
        String findRoomByRoomId(String roomId);
    }
    
    @Repository
    public class SpringChessLogDao implements ChessRepository {
        private SimpleJdbcInsert simpleJdbcInsert;
        private JdbcTemplate jdbcTemplate;
    
        public SpringChessLogDao(DataSource dataSource, JdbcTemplate jdbcTemplate) {
            this.simpleJdbcInsert = new SimpleJdbcInsert(dataSource)
                    .withTableName("chessGame")
                    .usingGeneratedKeyColumns("id");
            this.jdbcTemplate = jdbcTemplate;
        }
    
        public Long add(MoveRequestDto moveRequestDto) {
            SqlParameterSource parameterSource = new BeanPropertySqlParameterSource(moveRequestDto);
            return simpleJdbcInsert.executeAndReturnKey(parameterSource).longValue();
        }
    
        public List<CommandDto> find(String roomId) {
            String query = "select target, destination from chessgame where room_id = ? ORDER BY command_date ASC;";
            return jdbcTemplate.query(
                    query,
                    (resultSet, rowNum) -> {
                        CommandDto commandDto = new CommandDto(
                                roomId,
                                resultSet.getString("target"),
                                resultSet.getString("destination")
                        );
                        return commandDto;
                    }, roomId);
        }
    
        public void delete(String roomId) {
            String query = "DELETE FROM chessgame WHERE room_id = ?";
            this.jdbcTemplate.update(query, roomId);
        }
    
        public String findRoomByRoomId(String roomId) {
            String query = "select room_id from chessgame where room_id = ?";
            List<String> roomLog = jdbcTemplate.query(
                    query,
                    (resultSet, rowNum) -> resultSet.getString("room_id")
                    , roomId);
    
            if (Objects.isNull(roomLog)) {
                return null;
            }
            return roomId;
        }
    }
    
    ```

### 링크

[DAO 와 REPOSITORY 논쟁](http://aeternum.egloos.com/1160846)

## [MVC] Controller and RestController - 2

### 내용

- Controller 에 @RequestBody 애너테이션이 포함되어 있다고 볼 수 있다.
  - @RequestBody 를 사용하면 반환값으로 json 을 보낼 수 있다.
  - @Controller 는 model 을 포함한 view 를 반환한다.

### 링크

[@Controller와 @RestController 차이](https://mangkyu.tistory.com/49)

## [MVC] Model And View - 1

- 모델과 뷰를 같이 반환할 수 있도록 만들어진 클래스다.

  ```java
  @GetMapping("/chess/start")
      public ModelAndView startGame(@RequestParam("room") String id) {
          if (Objects.nonNull(springChessService.findRoomByRoomId(id))) {
              final ModelAndView modelAndView = new ModelAndView("main");
              modelAndView.addObject("interact", ROOM_NOT_EXIST);
              return modelAndView;
          }
  
          final ModelAndView modelAndView = new ModelAndView("index");
          modelAndView.addObject("roomId", id);
  
          return modelAndView;
      }
  ```

### 내용

- Controller 에 @RequestBody 애너테이션이 포함되어 있다고 볼 수 있다.
  - @RequestBody 를 사용하면 반환값으로 json 을 보낼 수 있다.
  - @Controller 는 model 을 포함한 view 를 반환한다.

## [MVC] Try-with-resources - 2

### 내용

- 예외 발생 시에도 안전하게 Connection 이 종료 되도록 처리하였다.

  ```java
  public void addLog(String roomId, String target, String destination) {
          java.sql.Connection connection = getConnection();
          String query = "INSERT INTO chessgame (room_id, target, destination) VALUES (?, ?, ?)";
          try (PreparedStatement pstmt = connection.prepareStatement(query);) {
              pstmt.setString(1, roomId);
              pstmt.setString(2, target);
              pstmt.setString(3, destination);
              pstmt.executeUpdate();
              closeConnection(connection);
          }
          catch (SQLException e) {
              Logger.getLogger("로그 추가 오류:" + e.getMessage());
              e.printStackTrace();
              closeConnection(connection);
          }
      }
  ```

### 링크

[Try-with-resources를 이용한 자원해제 처리](https://ryan-han.com/post/java/try_with_resources/)

[중첩 try with resources 는 어떻게 작동할까](https://multifrontgarden.tistory.com/192)

## [Spring] ExceptionHandler - 4

### 내용

- 이동 오류 발생 시 에러를 클라이언트에게 전달 해주도록 했다.

  ```java
  @ExceptionHandler({InvalidMoveException.class, IllegalRoomException.class, GameIsNotStartException.class, NoSuchCommandException.class})
      public ResponseEntity<ErrorMessageDto> moveFail(Exception e) {
          return ResponseEntity.badRequest().body(new ErrorMessageDto(e.getMessage()));
      }
  ```

- ExceptionHandler 를 사용하고 나니 비로소 레벨 1때 Custom Exception 을 만드는 것이 좋은 습관이라고 말했던 리뷰어의 코멘트가 생각났다.

- 원하는 예외를 손쉽게 이해하기 쉬운 코드로 만들 수 있다.

- JS 에서 예외 핸들러를 받아 경고창을 띄우는 기능으로 변경했다.

  ```javascript
  function postFetch(url, bodyData) {
      return fetch(url, {
          method: 'post',
          headers: {
              'Content-type': 'application/json',
              'Accept': 'application/json'
          },
          body: bodyData
      }).then(response => {
          if (response.status === 400) {
              exceptionHandler(response.json());
          }
          if (!response.ok) {
              throw new Error(response.status);
          }
          return response.json();
      });
  }
  
  function exceptionHandler(error) {
      error.then(data => {
          alert(data.errorMessage);
      });
  }
  ```

  

### 링크

[@ControllerAdvice, @ExceptionHandler를 이용한 예외처리 분리, 통합하기(Spring에서 예외 관리하는 방법, 실무에서는 어떻게?)](https://jeong-pro.tistory.com/195)

## [Spring] RequestMapping - 2

### 내용

- /chess 라는 prefix 를 지정하기 위해 RestController 에서 사용했다.

- Controller 는 main ("/") 로 이동하기 위해서 사용하지 않았다.

- URL 패턴은 디폴트 접미어 패턴이 적용되므로 아래 2개는 동일한 의미이다.

  ```java
  // 2개는 동일하다!
  @RequestMapping(value="/post")
  @RequestMapping(value={"/post", "/post/", "/post.*"})
  ```

- 똑같은 URL도 다르게 매핑할 수 있다.

  ```java
  // url이 /post인 요청 중 GET 메서드인 경우 호출됨
  @RequestMapping(value="/post", method=RequestMethod.GET)
  
  // url이 /post인 요청 중 POST 메서드인 경우 호출됨
  @RequestMapping(value="/post", method=RequestMethod.POST)
  ```

### 링크

[[Spring] @RequestMapping](https://joont92.github.io/spring/@RequestMapping/)

