# Java와 MySQL를 이용한 기프티콘 생성 및 관리 시스템 

2022년 8월 미니 프로젝트 [ Java와 MySQL를 이용한 기프티콘 생성 및 관리 시스템 ](https://github.com/12OneTwo12/mini-project)을 진행하며 경험하고자 한 것들을 이곳에 남깁니다.
  
### 목차  
  * #### [[ 프로젝트 기간 및 인원 ]](#프로젝트-기간-및-인원)  
  * #### [[ 프로젝트 목적 ]](#프로젝트-목적)  
  * #### [[ 사용 기술 ]](#사용-기술)  
  * #### [[ 시스템의 기능 ]](#시스템의-기능)  
  * #### [[ ERD ]](#erd)  
  * #### [[ 트러블슈팅 ]](#트러블슈팅)  
  * #### [[ 느낀 점 ]](#느낀-점)  
  * #### [[ 동작과정 ]](#동작과정)  
    
--------------------------------------------------------------------------------------------------------------
  
* ### 프로젝트 기간 및 인원  
  
프로젝트 기간: 2022년 8월 8일 ~ 2022년 8월 11일  
인원 : 정정일, 윤종현, 박철련, 최지웅 이하 총 4명
    
* ### 프로젝트 목적  
  
  * 팀원들과의 협업을 통해 직접 프로그래밍 협업을 경험  
  * 자바의 코드들을 직접 생각하고 활용하여 프로그램을 구축해보는 것을 직접 경험
  * 자바와 데이터베이스를 활용하여 데이터의 입출력 및 수정 삭제(CRUD)를 이해하고 활용
    
* ### 사용 기술
    
  * Java  
  * MySQL  
    
* ### 시스템의 기능  
  
  * 기프티콘 사용  
    
        기프티콘 일련 번호를 입력하고 사용 금액을 입력하면 데이터 베이스에서   
        일련 번호가 가지고 있던 보유 금액에서 사용 금액만큼 차감 후 DB에 결제 일시와 결제 금액을 기록에 남기는 기능  
        
  * 기프티콘 구매  
    
        기프티콘 구매를 원하는 금액을 입력하면 12자리 숫자를 랜덤으로   
        생성하여 일련 번호로 가지게 하고 원하는 금액만큼을 Amount로 갖게 한 후 DB의 gifticon 테이블에 저장 및 화면에 출력하는 기능
  
  * 잔액 조회  
    
        기프티콘 일련 번호를 입력하면 그 일련 번호를 가지고 있는 기프티콘의 잔액을 DB의 gifticon 테이블에서 가져와 출력하는 기능  
          
  * 결제 내역 확인  
    
        기프티콘 일련 번호를 입력하면 그 일련 번호를 가지고 있는 기프티콘의 결제 내역 데이터를 DB의 record테이블에서 가져와 출력하는 기능
          
  * 기프티콘 합산  
    
        두개의 기프티콘 일련 번호를 입력하면 각각의 기프티콘이 가지고 있던 금액을 합산한 금액을 가진 하나의 기프티콘 생성 및 DB에 저장하고 화면에 출력,  
        기존의 기프티콘 2개의 보유 금액을 0으로 만드는 기능.  
      
* ### ERD  
  
  ![image url](https://github.com/12OneTwo12/mini-project/blob/main/plan/gifticon2.png?raw=true) 
  ![image url](https://github.com/12OneTwo12/mini-project/blob/main/plan/gifticon3.png?raw=true)  
        
* ### 트러블슈팅  
  
    당연하게도 진행하면서 예외 상황들이 많았다.  
    다른 문제들은 비교적 손쉽게 해결할 수 있었는데 한가지 기억에 남는 예외 상황이 있었다.  
    ```java
    private PreparedStatement createStatement(Connection connection, String sql, Gifticon gifticon)
      throws SQLException {
      preparedStatement = connection.prepareStatement(sql);

      preparedStatement.setString(1, gifticon.getSerialNumber());
      return preparedStatement;
    }

    public boolean zeroAmount(Gifticon gifticon) {
      final String selectQuery = "UPDATE gifticon SET amount = -1 WHERE serial_number = ?;";

      try (Connection connection = Utils.getConnection();
          PreparedStatement preparedStatement = createStatement(connection, selectQuery, gifticon);) {
        preparedStatement.executeUpdate();
        return true;
      } catch (Exception e) {
        e.printStackTrace();
      }
      return false;
    }
    ```
    기프티콘 합산 기능을 구현하기 위해 DAO 클래스에 작성한 ```zeroAmount()``` 메소드를   
    다른 클래스에서 호출해도 DB에 있는 기프티콘의 값이 0으로 바뀌지 않는 예외가 발생한 것이다.  
      
    sql 명령문이 잘못된 것인지, 메소드 자체가 잘 못 작성된 것인지, 내가 상상 가능한 부분들을 전부 체크해 보았지만 찾지 못했다.  
    호출하는 곳을 바꿔보기도 하고, 직접 일련번호를 넣어보기도 했다.  
    직접 일련 번호를 넣었을때는 동작하는 것을 확인 했기에 메소드 자체에는 문제가 없다는 것을 확인 했는데 어디서 값이 제대로 전달 되지 않았는지를 찾지못해,  
    약 2시간 정도 씨름을 했다. 처음 겪어보는 막막한 예외상황에 답답해지기 시작했지만 마음을 가라앉히고 차분히 처음부터 살펴보기로 했다.  
      
    그래서 메소드가 호출되기 전까지의 과정에 하나하나 출력문을 넣어보고 실행 자체는 되는 것인지를 체크했는데,  
    실행 자체는 됐으나 ```zeroAmout()```가 호출되는 곳에서 메소드의 파라미터로 ```null```값이 들어가는 것을 확인했다.  
    그렇다면 어딘가에서 값을 잘못 불러왔다는 이야기가 됐는데 null을 본 순간 혹시? 라는 생각이 들어 파라미터로 들어가는 Gifticon클래스 부분을 확인해봤고,  
    문제는 이 부분에 있었음을 알게 됐다.  
    
    ```java  
    PreparedStatement createPreparedStatement(Connection connection, String sql, String SerialNumber)
        throws SQLException {
      preparedStatement = connection.prepareStatement(sql);
      preparedStatement.setString(1, SerialNumber);

      return preparedStatement;
    }

    public Gifticon findBySerialNumber(String SerialNumber) {

      final String selectQuery = "SELECT * FROM gifticon WHERE serial_number = ?;";

      try (Connection connection = Utils.getConnection();
          PreparedStatement preparedStatement = createPreparedStatement(connection, selectQuery, SerialNumber);
          ResultSet resultSet = preparedStatement.executeQuery();) {

        while (resultSet.next()) {
          Gifticon gifticon = new Gifticon(resultSet.getInt("gifticon_id"),
              resultSet.getDate("expiry_date").toLocalDate(), resultSet.getInt("amount"),
             // resultSet.getString("serial_number") // 이 부분이 없었다. );

          return gifticon;
        }
      } catch (Exception e) {
        e.printStackTrace();
      }

      return null;
    }  
    ```  
       
    기프티콘 합산을 위해 사용자가 일련 번호를 입력했을때 그 일련 번호로 된   
    Gifticon을 가져다 주는 메소드 ```findBySerialNumber()```가 자신의 serial_number를 다시 멤버 값으로 받게 코딩하지 않았기에  
    일련 번호로 조회했을때 가지고 있는 값은 정상적으로 전달 됐지만 스스로의 일련 번호 값은 가지지 못했던 것이다.  
    그렇기에 ```zeroAmount()```에는 null값이 전해지니 당연하게도 실행 되지 않는다고 느껴질 수 밖에 없었던 것이다.
    ```findBySerialNumber()```메소드에 ```resultSet.getString("serial_number")```을 추가해주고 Gifticon에 생성자를 하나 만들어주니 해결됐다.  
    
    이 부분을 해결해 냈을때 나는 속으로 막힌 혈관이 뚫린것과 같은 쾌감을 느꼈다.  
    
* ### 느낀 점  
    
   이번 프로젝트가 나에게는 첫 프로젝트였다.  
   생각보다 협업이라는 개념이 가져다주는 즐거움을 크기도 했고 어렵다고 느껴지는 부분도 있었다.  
   처음으로 느껴보는 내가 주체적이게 무언가를 만들어가는 프로그래밍 과정은 처음에는 막막했지만 진행하면서 쌓여가는 경험과 코딩들이 상당한 만족감과 즐거움을 가져다 주었다.  
   하기전에는 쉬울거라 생각했던 부분들도 막상 내가 직접하니 생각보다 쉽지 않았고, 그만큼 소중한 경험을 했다는 생각이 드는 프로젝트였다.  
     
* ### 동작과정  
  
  ![image url](https://github.com/12OneTwo12/mini-project/blob/main/plan/ezgif.com-gif-maker.gif?raw=true)  
