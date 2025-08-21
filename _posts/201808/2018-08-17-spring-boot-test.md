---
layout: post
title: How to do test in spring boot
categories: [spring boot, test]
---
# How to do test in spring boot

[引用](https://github.com/mbhave/tdd-with-spring-boot)

## Integration test

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class IntegrationTests {

	@Autowired
	private TestRestTemplate testRestTemplate;

	@Test
	public void getCar_WithName_ReturnsCar() {
		ResponseEntity<Car> responseEntity = this.testRestTemplate.getForEntity("/cars/{name}", Car.class, "prius");
		Car car = responseEntity.getBody();
		assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
		assertThat(car.getName()).isEqualTo("prius");
		assertThat(car.getType()).isEqualTo("hybrid");
	}

}

```

## controller

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class CarsControllerTests {

	@Autowired
	private MockMvc mockMvc;

	@MockBean
	private CarService carService;

	@Test
	public void getCar_WithName_ReturnsCar() throws Exception {
		when(this.carService.getCarDetails("prius")).thenReturn(new Car("prius", "hybrid"));
		this.mockMvc.perform(get("/cars/{name}", "prius"))
				.andExpect(status().isOk())
				.andExpect(jsonPath("name").value("prius"))
				.andExpect(jsonPath("type").value("hybrid"));
	}

	@Test
	public void getCar_NotFound_Returns404() throws Exception {
		when(this.carService.getCarDetails(any())).thenReturn(null);
		this.mockMvc.perform(get("/cars/{name}", "prius"))
				.andExpect(status().isNotFound());
	}

}
```


## service

```java
@RunWith(MockitoJUnitRunner.class)
public class CarServiceTest {

	@Mock
	private CarRepository carRepository;

	private CarService carService;

	@Before
	public void setUp() throws Exception {
		carService = new CarService(carRepository);
	}

	@Test
	public void getCarDetails_returnsCarInfo() {
		given(carRepository.findByName("prius")).willReturn(new Car("prius", "hybrid"));

		Car car = carService.getCarDetails("prius");

		assertThat(car.getName()).isEqualTo("prius");
		assertThat(car.getType()).isEqualTo("hybrid");
	}

	@Test(expected = CarNotFoundException.class)
	public void getCarDetails_whenCarNotFound() throws Exception {
		given(carRepository.findByName("prius")).willReturn(null);

		carService.getCarDetails("prius");
	}
}
```

## Repository

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class CarRepositoryTests {

	@Autowired
	private CarRepository repository;

	@Autowired
	private TestEntityManager testEntityManager;

	@Test
	public void findByName_ReturnsCar() throws Exception {
		Car savedCar = testEntityManager.persistFlushFind(new Car("prius", "hybrid"));
		Car car = this.repository.findByName("prius");
		assertThat(car.getName()).isEqualTo(savedCar.getName());
		assertThat(car.getType()).isEqualTo(savedCar.getType());
	}
}
```

## cache

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.NONE)
@AutoConfigureTestDatabase
public class CachingTest {

	@Autowired
	private CarService service;

	@MockBean
	private CarRepository repository;

	@Test
	public void getCar_ReturnsCachedValue() throws Exception {
		given(repository.findByName(anyString())).willReturn(new Car("prius", "hybrid"));
		service.getCarDetails("prius");
		service.getCarDetails("prius");
		verify(repository, times(1)).findByName("prius");
	}
}
```

```java
@Service
public class CarService {

	private CarRepository carRepository;

	public CarService(CarRepository carRepository) {
		this.carRepository = carRepository;
	}

	@Cacheable("cars")
	public Car getCarDetails(String name) {
		Car car = carRepository.findByName(name);
		if(car == null) {
			throw new CarNotFoundException();
		}
		return car;
	}
}
```