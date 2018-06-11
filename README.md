import java.io.File;
import java.io.FileNotFoundException;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.util.*;
import javafx.animation.Animation;
import javafx.animation.KeyFrame;
import javafx.animation.Timeline;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Alert.AlertType;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TextField;
import javafx.scene.control.TextInputDialog;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.scene.text.Text;
import javafx.stage.Stage;
import javafx.util.Duration;

public class AfekaInstruments extends javafx.application.Application{
	private BorderPane mainPane = new BorderPane();
	private Scene myWindow = new Scene(mainPane,750,300);
	private Button Gobtn = new Button("Go!");
	private Button btnAdd = new Button("Add");
	private Button btnDel = new Button("Delete");
	private Button btnClear = new Button("Clear");
	private Button btnLeft = new Button("<");
	private Button btnRight = new Button(">");
	private TextField txtType = new TextField();
	private TextField txtBrand = new TextField();
	private TextField txtPrice = new TextField();
	private TextField searchField = new TextField();
	private ArrayList<MusicalInstrument> allInstruments = new ArrayList<MusicalInstrument>();
	private ArrayList<MusicalInstrument> temp2 = (ArrayList<MusicalInstrument>) allInstruments.clone();
	private int counter = 0;
	@Override
	public void start(Stage primaryStage){
		File file = getInstrumentsFileFromUser();
		loadInstrumentsFromFile(file, allInstruments);
		primaryStage.setTitle("Afeka Instruments Music Store");
		setTop();
		setMid();
		setLeft();
		setRight();
		setBottom();
		btnRight.setOnAction(e ->{
			counter++;
			setMid();
		});
		btnLeft.setOnAction(e ->{
			counter--;
			setMid();
		});
		Gobtn.setOnAction(e ->{
			allInstruments = temp2;
			ArrayList<MusicalInstrument> temp = new ArrayList<MusicalInstrument>();
			for (int i = 0; i < allInstruments.size(); i++) {
				if (allInstruments.get(i).toString().contains(searchField.getText()))
					temp.add(allInstruments.get(i));
			}
			temp2 = (ArrayList<MusicalInstrument>) allInstruments.clone();
			allInstruments = temp;
			counter = 0;
			setMid();
		});
		primaryStage.setScene(myWindow);
		primaryStage.show();
	}

	public void setTop(){
		searchField.setPrefWidth(630);
		searchField.setPromptText("Search...");
		HBox layout = new HBox(10);
		layout.setPadding(new Insets(20,20,20,20));
		layout.getChildren().addAll(searchField,Gobtn);
		layout.setAlignment(Pos.CENTER);
		mainPane.setTop(layout);
	}
	@SuppressWarnings("static-access")
	public void setMid(){
		if (counter>allInstruments.size()-4)
			counter = 0;
		if (counter<0)
			counter = allInstruments.size()-1;
		GridPane grid = new GridPane();
		grid.setPadding(new Insets(10,10,10,10));
		grid.setVgap(20);
		grid.setHgap(10);
		Label lblType = new Label("Type: ");
		grid.setConstraints(lblType, 0, 0);
		txtType = new TextField(allInstruments.get(counter).getClass().getName());
		grid.setConstraints(txtType, 1, 0);
		Label lblBrand = new Label("Brand: ");
		grid.setConstraints(lblBrand, 0, 1);
		txtBrand = new TextField(allInstruments.get(counter).getBrand());
		grid.setConstraints(txtBrand, 1, 1);
		Label lblPrice = new Label("Price: ");
		grid.setConstraints(lblPrice, 0, 2);
		txtPrice = new TextField(Double.toString(allInstruments.get(counter).getPrice()));
		grid.setConstraints(txtPrice, 1, 2);
		grid.setConstraints(btnAdd, 0, 3);
		grid.setHalignment(btnDel, HPos.CENTER);
		grid.setConstraints(btnDel, 1, 3);
		grid.setConstraints(btnClear, 2, 3);
		grid.getChildren().addAll(lblType,txtType,lblBrand,txtBrand,lblPrice,txtPrice,btnAdd,btnDel,btnClear);
		grid.setAlignment(Pos.CENTER);
		mainPane.setCenter(grid);
	}
	public void setLeft(){
		HBox HBoxbtnLeft = new HBox();
		HBoxbtnLeft.setAlignment(Pos.CENTER_LEFT);
		HBoxbtnLeft.getChildren().add(btnLeft);
		mainPane.setLeft(HBoxbtnLeft);
	}
	public void setRight(){
		HBox HBoxbtnRight = new HBox();
		HBoxbtnRight.setAlignment(Pos.CENTER_RIGHT);
		HBoxbtnRight.getChildren().add(btnRight);
		mainPane.setRight(HBoxbtnRight);
	}
	public void setBottom(){
		Label lblTime = new Label(txtAnim().getText());
		HBox HBoxtxt = new HBox();
		HBoxtxt.setAlignment(Pos.BOTTOM_CENTER);
		HBoxtxt.getChildren().add(lblTime);
		mainPane.setBottom(HBoxtxt);
	}
	public Text txtAnim(){
		Text timetxt = new Text("drek");
		Timeline timeline = new Timeline();
		timeline.getKeyFrames().add(new KeyFrame(Duration.seconds(0.1),e -> {timetxt.setText(LocalTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss")));}));
		timeline.setCycleCount(Animation.INDEFINITE);
		timeline.play();
		return timetxt;
	}
	public static void main(String[] args) {
		launch(args);
		/*ArrayList<MusicalInstrument> allInstruments = new ArrayList<MusicalInstrument>();
        File file = getInstrumentsFileFromUser();

        loadInstrumentsFromFile(file, allInstruments);

        if(allInstruments.size() == 0) {
            System.out.println("There are no instruments in the store currently");
            return;
        }

        printInstruments(allInstruments);

        int different = getNumOfDifferentElements(allInstruments);

        System.out.println("\n\nDifferent Instruments: " + different);

        MusicalInstrument mostExpensive = getMostExpensiveInstrument(allInstruments);

        System.out.println("\n\nMost Expensive Instrument:\n" + mostExpensive);*/
	}

	public static File getInstrumentsFileFromUser(){
		boolean stopLoop = true;
		File file = null;
		try {
			do {
				TextInputDialog dialog = new TextInputDialog();
				dialog.setTitle("Confirmation");
				dialog.setHeaderText("Load Instruments From File");
				dialog.setContentText("Please enter file name:");
				Optional<String> result = dialog.showAndWait();
				String filepath = result.get();
				file = new File(filepath);
				stopLoop = file.exists() && file.canRead();
				if(!stopLoop){
					Alert alert = new Alert(AlertType.ERROR);
					alert.setTitle("Error");
					alert.setHeaderText("File Error!");
					alert.setContentText("Cannot read from file, please try again");
					alert.showAndWait();
				}
			}while (!stopLoop);

		} catch (Exception e) {
			System.exit(0);
		}
		return file;
	}

	public static void loadInstrumentsFromFile(File file, ArrayList<MusicalInstrument> allInstruments){
		Scanner scanner = null;

		try {

			scanner = new Scanner(file);

			addAllInstruments(allInstruments ,loadGuitars(scanner));

			addAllInstruments(allInstruments ,loadBassGuitars(scanner));

			addAllInstruments(allInstruments ,loadFlutes(scanner));

			addAllInstruments(allInstruments ,loadSaxophones(scanner));

		}catch (InputMismatchException | IllegalArgumentException ex){
			System.err.println("\n"+ ex.getMessage());
			System.exit(1);
		}catch (FileNotFoundException ex){
			System.err.println("\nFile Error! File was not found");
			System.exit(2);
		} finally {
			scanner.close();
		}
		//System.out.println("\nInstruments loaded from file successfully!\n");

	}

	public static ArrayList loadGuitars(Scanner scanner){
		int numOfInstruments = scanner.nextInt();
		ArrayList guitars = new ArrayList(numOfInstruments);

		for(int i = 0; i < numOfInstruments ; i++)
			guitars.add(new Guitar(scanner));

		return guitars;
	}

	public static ArrayList loadBassGuitars(Scanner scanner){
		int numOfInstruments = scanner.nextInt();
		ArrayList bassGuitars = new ArrayList(numOfInstruments);

		for(int i = 0; i < numOfInstruments ; i++)
			bassGuitars.add(new Bass(scanner));

		return bassGuitars;
	}

	public static ArrayList loadFlutes(Scanner scanner){
		int numOfInstruments = scanner.nextInt();
		ArrayList flutes = new ArrayList(numOfInstruments);

		for(int i = 0; i < numOfInstruments ; i++)
			flutes.add(new Flute(scanner));


		return flutes;
	}

	public static ArrayList loadSaxophones(Scanner scanner){
		int numOfInstruments = scanner.nextInt();
		ArrayList saxophones = new ArrayList(numOfInstruments);

		for(int i = 0; i < numOfInstruments ; i++)
			saxophones.add(new Saxophone(scanner));

		return saxophones;
	}

	public static void addAllInstruments(ArrayList instruments, ArrayList moreInstruments){
		for(int i = 0 ; i < moreInstruments.size() ; i++){
			instruments.add(moreInstruments.get(i));
		}
	}

	public static void printInstruments(ArrayList instruments){
		for(int i = 0 ; i < instruments.size() ; i++)
			System.out.println(instruments.get(i));
	}



	public static int getNumOfDifferentElements(ArrayList instruments){
		int numOfDifferentInstruments;
		ArrayList differentInstruments = new ArrayList();
		System.out.println();

		for(int i = 0 ; i < instruments.size() ; i++){
			if(!differentInstruments.contains((instruments.get(i)))){
				differentInstruments.add(instruments.get(i));
			}
		}

		if(differentInstruments.size() == 1)
			numOfDifferentInstruments = 0;

		else
			numOfDifferentInstruments = differentInstruments.size();


		return numOfDifferentInstruments;
	}

	public static MusicalInstrument getMostExpensiveInstrument(ArrayList instruments){
		double maxPrice = 0;
		MusicalInstrument mostExpensive = (MusicalInstrument) instruments.get(0);

		for(int i = 0 ; i < instruments.size() ; i++){
			MusicalInstrument temp = (MusicalInstrument)instruments.get(i);

			if(temp.getPrice() > maxPrice){
				maxPrice = temp.getPrice();
				mostExpensive = temp;
			}
		}

		return mostExpensive;
	}
}
