import java.io.File;
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

public class EducatorPanelApp {
    private static final String[] ALLOWED_FORMATS = {"mp4", "MOV"};

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        // Initialize an educator
        Educator educator = new Educator("John Doe");
        Database database = new Database();
        
        boolean running = true;
        while (running) {
            System.out.println("\nWelcome to the Educator Panel, " + educator.getName() + "!");
            System.out.println("1. Upload Video");
            System.out.println("2. View Analytics");
            System.out.println("3. Exit");

            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    uploadVideo(scanner, educator, database);
                    break;
                    
                case 2:
                    educator.viewStatistics(database);
                    break;

                case 3:
                    running = false;
                    break;

                default:
                    System.out.println("Invalid choice, please try again.");
            }
        }

        scanner.close();
    }

    private static void uploadVideo(Scanner scanner, Educator educator, Database database) {
        System.out.print("Enter video file path: ");
        String filePath = scanner.nextLine();

        File file = new File(filePath);
        if (!isValidFormat(file)) {
            System.out.println("Video format is not valid. Allowed formats are mp4, MOV.");
            return;
        }
        
        if (!file.exists()) {
            System.out.println("File does not exist at the specified path: " + filePath);
            return;
        }

        System.out.print("Enter video title: ");
        String title = scanner.nextLine();

        System.out.print("Enter video category: ");
        String category = scanner.nextLine();
        
        try {
            Video video = new Video(filePath, title, category);
            if (video.meetsGuidelines()) { // Changed from private to package-private
                educator.uploadVideo(video, database);
            } else {
                System.out.println("The uploaded video does not meet the guidelines.");
            }
        } catch (Exception e) {
            System.err.println(e.getMessage());
        }
    }

        public static boolean isValidFormat(File file) {
        String extension = getFileExtension(file.getName());
        for (String format : ALLOWED_FORMATS) {
            if (format.equalsIgnoreCase(extension)) {
                return true;
            }
        }
        return false;
    }

    public static String getFileExtension(String fileName) {
        int lastIndexOfDot = fileName.lastIndexOf('.');
        if (lastIndexOfDot == -1 || lastIndexOfDot == fileName.length() - 1) {
            return "";
        }
        return fileName.substring(lastIndexOfDot + 1);
    }
}

class Educator {
    public String name;

    public Educator(String name) {
        this.name = name;
    }

    public void uploadVideo(Video video, Database database) throws Exception {
        if (!video.isValidFormat() || !video.meetsGuidelines()) { // Changed from private to package-private
            throw new Exception("Fix to match requirements.");
        }
        
        database.addVideo(video);
    }

    public void viewStatistics(Database database) {
        for (Map.Entry<String, Video> entry : database.getVideos().entrySet()) {
            System.out.println("Title: " + entry.getValue().getTitle());
            System.out.println("Category: " + entry.getValue().getCategory());
            System.out.println("Views: " + entry.getValue().getViewCount());
            System.out.println();
        }
    }

    public String getName() {
        return name;
    }
}

class Video {
    public final File file;
    public final String title;
    public final String category;
    public int viewCount = 0;

    public Video(String filePath, String title, String category) throws Exception {
        this.file = new File(filePath);
      

        
        this.title = title;
        this.category = category;
    }

    public boolean isValidFormat() {
        return EducatorPanelApp.isValidFormat(this.file);
    }

    public String getTitle() {
        return title;
    }

    public String getCategory() {
        return category;
    }

    public int getViewCount() {
        return viewCount;
    }

    public void incrementViewCount(int views) {
        this.viewCount += views;
    }

    boolean meetsGuidelines() { // Changed from private to package-private
        // Add your logic to check if the video content meets guidelines
        // For now, it's a placeholder that always returns true.
        return true;
    }
}

class Database {
    public Map<String, Video> videos = new HashMap<>();

    public void addVideo(Video video) {
        videos.put(video.getTitle(), video);
    }

    public Map<String, Video> getVideos() {
        return videos;
    }
}

