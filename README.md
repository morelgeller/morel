import java.io.File;
import java.io.FileNotFoundException;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.util.*;
import javafx.animation.Animation;
import javafx.animation.KeyFrame;
import javafx.animation.Timeline;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Alert.AlertType;
import javafx.scene.control.Button;
import javafx.scene.control.ComboBox;
import javafx.scene.control.Label;
import javafx.scene.control.TextField;
import javafx.scene.control.TextInputDialog;
import javafx.scene.input.KeyCode;
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
	private ArrayList<MusicalInstrument> temp2 = new ArrayList<MusicalInstrument>();
	private File file;
	private int counter = 0;
	@SuppressWarnings({ "unchecked", "static-access" })
	@Override
	public void start(Stage primaryStage){
		file = getInstrumentsFileFromUser();
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
			if (searchField.getText()=="")
				setMid();
			else
			{
				temp2 = (ArrayList<MusicalInstrument>) allInstruments.clone();
				ArrayList<MusicalInstrument> temp = new ArrayList<MusicalInstrument>();
				for (int i = 0; i < allInstruments.size(); i++) 
				{
					String one = allInstruments.get(i).toString();
					String two = searchField.getText();
					if (one.contains(two))
						temp.add(allInstruments.get(i));
				}
				allInstruments = temp;
				counter = 0;
				setMid();
			}
		});
		searchField.setOnKeyPressed(e -> {
			//if (e.getCode() == KeyCode.ENTER)
				////////Gobtn.getOnAction();
		});
		btnDel.setOnAction(e ->{
			allInstruments.remove(counter);
			setMid();
		});
		searchField.setOnKeyPressed(e -> {
			//if (e.getCode() == KeyCode.DELETE)
				////////Gobtn.getOnAction(); על המסך ולא על הטקסטפילד
		});
		btnClear.setOnAction(e -> {
			allInstruments.clear();
			setMid();
		});
		btnAdd.setOnAction(e ->{
			//פותח חלון חדש עם קומבובוקס
			Stage addStage = new Stage();
			BorderPane addPane = new BorderPane();
		    Scene addWin = new Scene(addPane,600,300);
		    HBox layout = new HBox(10);
		    ObservableList<String> options = FXCollections.observableArrayList("Guitar","Bass","Flute","Saxophone");
		    ComboBox<String> cm = new ComboBox<String>(options);
		    cm.setPromptText("Choose Instrument Type Here");
			layout.setPadding(new Insets(20,20,20,20));
			layout.getChildren().add(cm);
			layout.setAlignment(Pos.CENTER);
			cm.setOnAction(r -> {
				//layout.setAlignment(Pos.TOP_CENTER);
				GridPane grid = new GridPane();
				grid.setPadding(new Insets(10,10,10,10));
				grid.setVgap(20);
				grid.setHgap(10);
				Label lblBrand = new Label("Brand: ");
				grid.setConstraints(lblBrand, 0, 0);
				TextField txtBrandGuitar = new TextField();
				txtBrandGuitar.setPromptText("Ex: Gibson");
				grid.setConstraints(txtBrandGuitar, 1, 0);
				Label lblPrice = new Label("Price: ");
				grid.setConstraints(lblPrice, 0, 1);
				TextField txtPriceGuitar = new TextField();
				txtPriceGuitar.setPromptText("Ex: 7500");
				grid.setConstraints(txtPriceGuitar, 1, 1);
				Label lblNumOfString = new Label("Number of Strings: ");
				grid.setConstraints(lblNumOfString, 0, 2);
				TextField txtNumOfString = new TextField();
				txtNumOfString.setPromptText("Ex: 6");
				grid.setConstraints(txtNumOfString, 1, 2);
				Label lblType = new Label("Guitar Type: ");
				grid.setConstraints(lblType, 0, 3);
			    ObservableList<String> optionsGuitar = FXCollections.observableArrayList("Classic","Acoustic","Electric");
				ComboBox<String> cmGuitar = new ComboBox<String>(optionsGuitar);
				cmGuitar.setPromptText("Type");
				grid.setConstraints(cmGuitar, 1, 3);
				Button btnAdd = new Button("Add");
				grid.setConstraints(btnAdd, 1, 4);
				grid.setAlignment(Pos.CENTER);
				//addPane.setTop(layout);
				//layout.setAlignment(Pos.TOP_CENTER);
				addPane.setTop(grid);
			});
			addPane.setCenter(layout);
		    addStage.setScene(addWin);
			addStage.show();
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
		if (allInstruments.size()==0){
			txtType.setText("");
			txtBrand.setText("");
			txtPrice.setText("");
		}
		else
		{
		if (counter>=allInstruments.size())
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
