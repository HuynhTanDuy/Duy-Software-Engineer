# Contracts

![](images/contracts.jpg)

1.	__Khái niệm__
	Laravel contracts là một loạt các interface được định nghĩa trong Laravel framework. Ví dụ, *Illuminate\Contracts\Mail\Mailer* contract định nghĩa 1 interface Mailer bao gồm các phương thức cần thiết cho việc gửi mail.
	```
	interface Mailer
	{
       public function raw($text, $callback);
       public function send($view, array $data, $callback);
       public function failures();
    }
	```
	Ta có thể inject Interface này vào một class bất kì để sử dụng service Mailer của Laravel, phần việc implement interface nó đã có Laravel lo :v. Hoặc nếu không thích, ta có thể tự viết 1 service khác, dĩ nhiên phải thỏa mãn 'contract' là triển khai đầy đủ các phương thức đã khai báo ở Interface Mailer. Rồi tiến hành đăng ký interface Mailer với Service tự viết của chúng ta cho Service Container.
2. __Cách hoạt động của Contracts__ 
	Hãy lấy ví dụ về Contract Mailer đã nêu ở trên. Ta sẽ tìm được phần implemention của nó tại *Illuminate\Mail\Mailer.php*. 
	```
	use Illuminate\Contracts\Mail\Mailer as MailerContract;
	use Illuminate\Contracts\Mail\MailQueue as MailQueueContract;
	class Mailer implements MailerContract, MailQueueContract
	{
		public function raw($text, $callback)
    	{
        	return $this->send(['raw' => $text], [], $callback);
    	}
    	public function send($view, array $data, $callback)
    	{
        	list($view, $plain, $raw) = $this->parseView($view);
        	$data['message'] = $message = $this->createMessage();
        	$this->addContent($message, $view, $plain, $raw, $data);
        	$this->callMessageBuilder($callback, $message);
        	if (isset($this->to['address'])) {
            	$message->to($this->to['address'], $this->to['name'], true);
            }
            $message = $message->getSwiftMessage();
            return $this->sendSwiftMessage($message);
    	}
    	public function failures()
    	{
        	return $this->failedRecipients;
    	}
	}
	```

	Trong *Illuminate\Mail\MailServiceProvider* ta tìm được nơi đăng ký interface Mailer map với implemention Mailer ở Service Container.
	Từ đây, khi thực hiện dependency injection với interface Mailer, ta sẽ toàn quyền sử dụng implemention Mailer, nhờ vào magic của Service Container.  

3.	__Contracts và Facades__
	Contracts và Facades là những tư tưởng khá mới mẻ, đại diện cho sự thú vị và tiến bộ của Laravel Framework. Về bản chất, cả 2 đều cung cấp cho ta một phương tiện đơn giản, tiện lợi, nhanh chóng để làm việc với Service Container- Trái tim của Laravel. Hầu hết các Facade được Laravel cung cấp sẵn cũng sẽ có 1 Contract tương đương. Tuy vậy, cả 2 vẫn có những điểm khác biệt: 
	 - Facades không cần type-hint, ta sử dụng một cách nhanh chóng như *Auth::check* chẳng hạn, việc xử lý đằng sau do Laravel đảm nhận.
	 - Ngược lại, Contracts yêu cầu ta phải type-hint, thực hiện dependency injection cho nó ở class ta muốn sử dụng.Nhờ vậy, Contracts cho phép developer linh hoạt hơn trong việc định nghĩa các dependency ( dĩ nhiên là tốn công hơn so với Facades :v).

#References 
https://laravel.com/docs/6.x/contracts


 


