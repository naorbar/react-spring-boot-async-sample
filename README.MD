# Sample Async call with spring-boot and React

## The controller looks like this:

	@GetMapping("/sync")
	public ResponseEntity<?> sync() {
		System.out.println("Received a sync request");
		return ResponseEntity.ok("sync ok");
	}

	@GetMapping("/async")
	public DeferredResult<ResponseEntity<?>> async() {
		System.out.println("Received an async request");
		DeferredResult<ResponseEntity<?>> output = new DeferredResult<>();

		ForkJoinPool.commonPool().submit(() -> {
			System.out.println("Processing in separate thread");
			try {
				Thread.sleep(6000);
			} catch (InterruptedException e) {
				//
			}
			output.setResult(ResponseEntity.ok("async ok"));
		});

		System.out.println("servlet thread freed");
		return output;
	}
	
## The React code looks like this:	

	class App extends Component {

		constructor() {
			super();
		}

		componentDidMount() {
			this.callAsyncFromServer();
			...
		}

		callAsyncFromServer() {
			console.log("Calling async request on server...");
			fetch("/async")
		  .then(res => res)
		  .then(
			(result) => {
				alert("Result: " + result);
			},
			(error) => {
				alert("Error Result: " + error);
			}
		  )
		}
		
		...
		
	}
