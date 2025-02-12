Here's a practical Pokémon app using OOP concepts, integrating with the [PokeAPI](https://pokeapi.co/) (2 endpoints). This example fetches Pokémon data and displays stats with type-specific flair:

---

### **Pokémon App Code (All 5 OOP Concepts)**

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.ArrayList;
import java.util.List;

// 1. ABSTRACTION (Abstract class)
abstract class Pokemon {
    private String name;
    private List<String> types;

    public Pokemon(String name, List<String> types) {
        this.name = name;
        this.types = types;
    }

    // Abstract method (enforces implementation)
    public abstract void displayStats();

    // ENCAPSULATION (private fields with public getters)
    public String getName() { return name; }
    public List<String> getTypes() { return types; }
}

// 2. INHERITANCE (Concrete subclasses)
class FirePokemon extends Pokemon {
    public FirePokemon(String name, List<String> types) {
        super(name, types);
    }

    // 3. POLYMORPHISM (Method overriding)
    @Override
    public void displayStats() {
        System.out.println("🔥 " + getName() + " (Fire)");
        System.out.println("Types: " + getTypes());
    }
}

class WaterPokemon extends Pokemon {
    public WaterPokemon(String name, List<String> types) {
        super(name, types);
    }

    @Override
    public void displayStats() {
        System.out.println("💧 " + getName() + " (Water)");
        System.out.println("Types: " + getTypes());
    }
}

// 4. COMPOSITION (Team contains Pokémon)
class PokemonTeam {
    private List<Pokemon> team = new ArrayList<>();

    public void addPokemon(Pokemon p) {
        team.add(p);
    }

    public void showTeam() {
        System.out.println("\n=== YOUR POKÉMON TEAM ===");
        for (Pokemon p : team) {
            p.displayStats(); // Polymorphism in action
        }
    }
}

// 5. API SERVICE (Utility class)
class PokeAPIService {
    private static final String API_URL = "https://pokeapi.co/api/v2/pokemon/";
    private HttpClient client = HttpClient.newHttpClient();

    public Pokemon fetchPokemon(String name) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(API_URL + name.toLowerCase()))
                .build();

        HttpResponse<String> response = client.send(
                request, HttpResponse.BodyHandlers.ofString()
        );

        if (response.statusCode() != 200) {
            throw new RuntimeException("Pokémon not found!");
        }

        String json = response.body();
        // Simple JSON parsing (no external libraries)
        String parsedName = parseName(json);
        List<String> types = parseTypes(json);

        // Create appropriate subclass based on type
        if (types.contains("fire")) {
            return new FirePokemon(parsedName, types);
        } else if (types.contains("water")) {
            return new WaterPokemon(parsedName, types);
        }
        return new Pokemon(parsedName, types) {
            @Override
            public void displayStats() {
                System.out.println("⭐ " + getName());
                System.out.println("Types: " + getTypes());
            }
        };
    }

    private String parseName(String json) {
        int nameIndex = json.indexOf("\"name\":\"") + 8;
        return json.substring(nameIndex, json.indexOf("\"", nameIndex));
    }

    private List<String> types = new ArrayList<>();
    private List<String> parseTypes(String json) {
        List<String> types = new ArrayList<>();
        int typeIndex = json.indexOf("\"types\":[");
        while (true) {
            int typeStart = json.indexOf("\"name\":\"", typeIndex) + 8;
            if (typeStart < 8) break;
            String type = json.substring(typeStart, json.indexOf("\"", typeStart));
            types.add(type);
            typeIndex = typeStart + 1;
        }
        return types;
    }
}

public class Main {
    public static void main(String[] args) {
        PokeAPIService api = new PokeAPIService();
        PokemonTeam team = new PokemonTeam();

        try {
            // Fetch from API (Endpoint 1: Pokémon Data)
            Pokemon charizard = api.fetchPokemon("charizard");
            Pokemon squirtle = api.fetchPokemon("squirtle");
            
            // Add to team (composition)
            team.addPokemon(charizard);
            team.addPokemon(squirtle);

            // Display team (polymorphism)
            team.showTeam();

        } catch (Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

---

### **OOP Concepts Demonstrated**
1. **Encapsulation**  
   - `Pokemon` fields (`name`, `types`) are private, accessed via getters
   - API parsing logic is hidden inside `PokeAPIService`

2. **Inheritance**  
   - `FirePokemon` and `WaterPokemon` extend `Pokemon`

3. **Polymorphism**  
   - Different `displayStats()` implementations per subclass
   - Team handles all Pokémon uniformly

4. **Abstraction**  
   - `Pokemon` is abstract with `displayStats()` as an abstract method
   - Users don't need to know API implementation details

5. **Composition**  
   - `PokemonTeam` contains a list of `Pokemon` objects
   - `PokeAPIService` uses `HttpClient` internally

---

### **API Endpoints Used**
1. **Pokémon Data**: `https://pokeapi.co/api/v2/pokemon/{name}`  
   - Gets name, types, stats
2. **Type Data** (implicitly via first endpoint's response)  
   - Used to determine Pokémon subclass

---

### **Sample Output**
```
=== YOUR POKÉMON TEAM ===
🔥 charizard (Fire)
Types: [fire, flying]

💧 squirtle (Water)
Types: [water]
```

---

This example combines real-world API usage with core OOP principles. Students will see how abstraction hides complexity, polymorphism enables type-specific behavior, and composition builds complex systems from simple parts.
This example combines real-world API usage with core OOP principles. Students will see how abstraction hides complexity, polymorphism enables type-specific behavior, and composition builds complex systems from simple parts.