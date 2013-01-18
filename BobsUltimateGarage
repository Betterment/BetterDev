package com.betterment.service.cashtracking.impl;

import java.math.BigDecimal;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

import org.apache.commons.collections.SetUtils;
import org.apache.commons.collections.map.MultiKeyMap;
import org.apache.commons.lang.builder.EqualsBuilder;
import org.apache.commons.lang.builder.HashCodeBuilder;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.joda.time.DateTime;
import org.springframework.util.Assert;

import com.google.common.base.Function;
import com.google.common.collect.Sets;

/**
 * Bob's Garage : procedural conditions vs functional rules engine.
 * 
 * This is a huge class with two section. First uses if/else conditionals. The second uses functional idioms to make our
 * code more elegant and perhaps even more powerful!
 * 
 * @author Yuriy M Goldman (yuriy@betterment.com)
 */
public class BobsUltimateGarage {

    static final Log log = LogFactory.getLog(BobsUltimateGarage.class);

    public static void main(String[] args) {
        BobsUltimateGarage garage = new BobsUltimateGarage();
        TicketRequest request = new TicketRequest(new DateTime(2013, 1, 18, 1, 30, 0, 0), "pirate", 1902);
        TicketResponse response1 = garage.evaluateProcedurally(request);
        log.info("Response 1: " + response1);

        garage.initialize();
        TicketResponse response2 = garage.evaluateFunctionally(request);
        log.info("Response 2: " + response2);
        Assert.isTrue(response1.equals(response2),
                "Dev Error: As part of this example, both recommendations should be the same!");
    }

    //For data models that don't require polymorphic behavior, Enums are simpler to work with
    //CarType illustrates some Enum best practices as well...
    enum CarType {
        //Using Guava's sets
        //Car models are defined in-line so you don't forget to define models for each supported car type when adding new car types
        //see Effective Java chapters on Enums for further reading
        FERARI(CarClass.SPORT, Sets.newHashSet("california", "458 italis", "f12 berlinetta", "ff")),
        LEXUS(CarClass.LUXURY, Sets.newHashSet("is", "gs", "ls")),
        OLDSMOBILE(CarClass.CLASSIC, Sets.newHashSet("pirate", "55", "defender"));

        CarClass carClass;
        Set<String> carModels;

        CarType(CarClass carClass, Set<String> carModels) {
            this.carClass = carClass;
            this.carModels = carModels;
        }

        CarClass getCarClass() {
            return this.carClass;
        }

        @SuppressWarnings("unchecked")
        //return view only
        Set<String> getCarModels() {
            return SetUtils.unmodifiableSet(this.carModels);
        }

        static CarType fromModel(String model) {
            for (CarType carType : values()) {
                if (carType.carModels.contains(model)) {
                    return carType;
                }
            }
            throw new IllegalArgumentException("Unknown car model: " + model);
        }
    }

    enum TimeOfDay {
        MORNING,
        AFTERNOON,
        EVENING,
        ANY;

        static TimeOfDay fromDateTime(DateTime dateTime) {
            //too lazy to process dateTime properly...
            return MORNING;
        }
    }

    enum CarClass {
        LUXURY,
        SPORT,
        CLASSIC
    }

    enum GarageLevel {
        LEVEL1,
        LEVEL2,
        LEVEL3
    }

    enum GarageSection {
        SECTION1,
        SECTION2,
        SECTION3
    }

    /**
     * Composition over inheritance preferred for this example Immutability also preferred.
     */
    static class Car {
        final CarType carType;
        final int makeYear;

        Car(CarType carType, int makeYear) {
            this.carType = carType;
            this.makeYear = makeYear;
        }

        CarClass getCarClass() {
            return this.carType.getCarClass();
        }

        /**
         * Using apache commons HashCodeBuilder
         */
        @Override
        public int hashCode() {
            return new HashCodeBuilder().append(this.carType).append(this.makeYear).hashCode();
        }

        /**
         * Using apache commons EqualsBuilder
         */
        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }
            if ((obj == null) || !(obj instanceof Car)) {
                return false;
            }
            Car other = (Car) obj;
            return new EqualsBuilder().append(this.carType, other.carType).append(this.makeYear, other.makeYear)
                    .isEquals();
        }

        @Override
        public String toString() {
            return "Car [carType=" + carType + ", makeYear=" + makeYear + "]";
        }
    }

    //immutable, threadsafe container for hourly estimates
    static class HourlyEstimate {
        final int numHoursEst;
        final BigDecimal avgHourlyPrice;

        HourlyEstimate(int numHoursEst, BigDecimal avgHourlyPrice) {
            this.numHoursEst = numHoursEst;
            this.avgHourlyPrice = avgHourlyPrice;
        }

        /**
         * Using apache commons HashCodeBuilder
         */
        @Override
        public int hashCode() {
            return new HashCodeBuilder().append(numHoursEst).append(avgHourlyPrice).hashCode();
        }

        /**
         * Using apache commons EqualsBuilder
         */
        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }
            if ((obj == null) || !(obj instanceof HourlyEstimate)) {
                return false;
            }
            HourlyEstimate other = (HourlyEstimate) obj;
            return new EqualsBuilder().append(this.numHoursEst, other.numHoursEst)
                    .append(this.avgHourlyPrice, other.avgHourlyPrice).isEquals();
        }

        @Override
        public String toString() {
            return "HourlyEstimate [numHoursEst=" + numHoursEst + ", avgHourlyPrice=" + avgHourlyPrice + "]";
        }
    }

    static class Recommendation {
        final GarageLevelSection gls;
        final HourlyEstimate estimate;

        Recommendation(GarageLevelSection gls, HourlyEstimate estimate) {
            this.gls = gls;
            this.estimate = estimate;
        }

        /**
         * Using apache commons HashCodeBuilder
         */
        @Override
        public int hashCode() {
            return new HashCodeBuilder().append(this.gls).append(this.estimate).hashCode();
        }

        /**
         * Using apache commons EqualsBuilder
         */
        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }
            if ((obj == null) || !(obj instanceof Recommendation)) {
                return false;
            }
            Recommendation other = (Recommendation) obj;
            return new EqualsBuilder().append(this.gls, other.gls).append(this.estimate, other.estimate).isEquals();
        }

        @Override
        public String toString() {
            return "Recommendation [gls=" + gls + ", estimate=" + estimate + "]";
        }
    }

    static class TicketRequest {
        final DateTime entryTimestamp;
        final String modelName;
        final int makeYear;

        TicketRequest(DateTime entryTimestamp, String modelName, int makeYear) {
            this.entryTimestamp = entryTimestamp;
            this.modelName = modelName;
            this.makeYear = makeYear;
        }

        @Override
        public String toString() {
            return "TicketRequest [entryTimestamp=" + entryTimestamp + ", modelName=" + modelName + ", makeYear="
                    + makeYear + "]";
        }
    }

    static class TicketResponse {

        final DateTime entryTimestamp;
        final Car car;
        final Recommendation recommendation1;
        final Recommendation recommendation2;

        TicketResponse(DateTime entryTimestamp, Car car, Recommendation r1, Recommendation r2) {
            this.entryTimestamp = entryTimestamp;
            this.car = car;
            this.recommendation1 = r1;
            this.recommendation2 = r2;
        }

        /**
         * Using apache commons HashCodeBuilder
         */
        @Override
        public int hashCode() {
            return new HashCodeBuilder().append(this.entryTimestamp).append(this.car).append(this.recommendation1)
                    .append(this.recommendation2).hashCode();
        }

        /**
         * Using apache commons EqualsBuilder
         */
        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }
            if ((obj == null) || !(obj instanceof TicketResponse)) {
                return false;
            }
            TicketResponse other = (TicketResponse) obj;
            return new EqualsBuilder().append(this.car, other.car).append(this.entryTimestamp, other.entryTimestamp)
                    .append(this.recommendation1, other.recommendation1)
                    .append(this.recommendation2, other.recommendation2).isEquals();
        }

        @Override
        public String toString() {
            return "TicketResponse [entryTimestamp=" + entryTimestamp + ", car=" + car + ", recommendation1="
                    + recommendation1 + ", recommendation2=" + recommendation2 + "]";
        }
    }

    /**
     * Everything is done procedurally to illustrate a point. Of course, some refactoring will go a long way. Design
     * patterns such as Strategy along-side Builder can also likely be a "good enough" solution. This example gives you
     * more tool in your arsenal to choose from.
     */
    TicketResponse evaluateProcedurally(TicketRequest request) {
        log.info("Processing procedurally: " + request);
        CarType carType = CarType.fromModel(request.modelName);
        CarClass carClass = carType.getCarClass();
        Car car = new Car(carType, request.makeYear);

        TimeOfDay timeOfDay = TimeOfDay.fromDateTime(request.entryTimestamp);
        Recommendation r1 = null;
        Recommendation r2 = null;

        //with just two variables, we have two levels of nested if/elses
        if (CarClass.CLASSIC.equals(carClass)) {
            //Bob doesn't value classics much...
            if (TimeOfDay.MORNING.equals(timeOfDay)) {
                //this can be refactored more... for compostional object building, Builder method is preferred.
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL1,
                        GarageSection.SECTION1);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL1, GarageSection.SECTION1), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION1);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION1), estimate2);
            } else {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL1,
                        GarageSection.SECTION2);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL1, GarageSection.SECTION2), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION2);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION2), estimate2);
            }

        } else if (CarClass.LUXURY.equals(carClass)) {
            if (TimeOfDay.MORNING.equals(timeOfDay)) {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL3,
                        GarageSection.SECTION1);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL3, GarageSection.SECTION1), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION1);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION1), estimate2);

            } else if (TimeOfDay.AFTERNOON.equals(timeOfDay)) {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL3,
                        GarageSection.SECTION2);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL3, GarageSection.SECTION2), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION2);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION2), estimate2);

            } else if (TimeOfDay.EVENING.equals(timeOfDay)) {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL3,
                        GarageSection.SECTION3);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL3, GarageSection.SECTION3), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION3);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION3), estimate2);
            }

        } else if (CarClass.SPORT.equals(carClass)) {
            if (TimeOfDay.EVENING.equals(timeOfDay)) {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL3,
                        GarageSection.SECTION3);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL3, GarageSection.SECTION3), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION3);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION3), estimate2);
            } else {
                HourlyEstimate estimate1 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL2,
                        GarageSection.SECTION2);
                r1 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL2, GarageSection.SECTION2), estimate1);

                HourlyEstimate estimate2 = calculateHourlyEstimateImperatively(timeOfDay, GarageLevel.LEVEL3,
                        GarageSection.SECTION2);
                r2 = new Recommendation(new GarageLevelSection(GarageLevel.LEVEL3, GarageSection.SECTION2), estimate2);
            }

        } else {
            throw new IllegalArgumentException("Oops, unsupported car class: " + carClass + " for car type: " + carType);
        }

        return new TicketResponse(request.entryTimestamp, car, r1, r2);
    }

    HourlyEstimate calculateHourlyEstimateImperatively(TimeOfDay timeOfDay, GarageLevel level, GarageSection section) {
        int hours = getHourEstimateFromTimeOfDay(timeOfDay);
        BigDecimal price = getHourlyRate(level, section);
        return new HourlyEstimate(hours, price);
    }

    /**
     * If TimeOfDay changes, this method will have to be updated
     * 
     * @param timeOfDay
     * @return
     */
    private int getHourEstimateFromTimeOfDay(TimeOfDay timeOfDay) {
        int result = 1; //made up avg stay
        if (TimeOfDay.AFTERNOON.equals(timeOfDay)) {
            result = 2; //maybe an errand?
        } else if (TimeOfDay.MORNING.equals(timeOfDay)) {
            result = 8; //8-hr work day?
        } else if (TimeOfDay.EVENING.equals(timeOfDay)) {
            result = 12; //overnight?
        }
        return result;
    }

    /**
     * Illustrates different rules per Garage Level per Garage Section.
     * 
     * Observe that as the system grows and requirements change you will be tempted to introduce new methods perform a
     * similar if/else walk. This is generally a poor practice, since you ma forget to update the method's logic should
     * you introduce a new enum into the mix.
     * 
     * @param timeOfDay
     * @return
     */
    private BigDecimal getHourlyRate(GarageLevel level, GarageSection section) {
        BigDecimal result = new BigDecimal(5); //$5.00/hr default
        if (GarageLevel.LEVEL1.equals(level)) {
            if (GarageSection.SECTION1.equals(section)) {
                result = new BigDecimal(5);
            } else {
                result = new BigDecimal(7);
            }
        } else if (GarageLevel.LEVEL2.equals(level)) {
            if (GarageSection.SECTION3.equals(section)) {
                result = new BigDecimal(10);
            } else {
                result = new BigDecimal(5);
            }
        } else if (GarageLevel.LEVEL3.equals(level)) {
            result = new BigDecimal(10);
        }
        return result;
    }

    // END OF IMPRERATIVE SECTION
    // START OF FUNCTIONAL SECTION (with multi-key maps!!)

    private final Map<CarClass, BusinessRule> BIZ_RULES = new HashMap<CarClass, BusinessRule>();
    private final MultiKeyMap RECOMMENDATIONS = new MultiKeyMap();

    private final Function<DateTime, TimeOfDay> simpleDateConverterFunction = new Function<DateTime, BobsUltimateGarage.TimeOfDay>() {
        @Override
        public TimeOfDay apply(DateTime dateTime) {
            return TimeOfDay.fromDateTime(dateTime);
        }
    };

    private final Function<DateTime, TimeOfDay> complexDateConverterFunction = new Function<DateTime, TimeOfDay>() {
        @Override
        public TimeOfDay apply(DateTime dateTime) {
            TimeOfDay result = TimeOfDay.ANY;
            int hour = dateTime.hourOfDay().get();
            if (hour < 12) {
                result = TimeOfDay.MORNING;
            } else if (hour < 18) {
                result = TimeOfDay.AFTERNOON;
            } else if (hour <= 24) {
                result = TimeOfDay.EVENING;
            }
            return result;
        }
    };

    //no minimum stay
    private final Function<TimeOfDay, Integer> simpleDurationEstimateFunction = new Function<TimeOfDay, Integer>() {
        @Override
        public Integer apply(TimeOfDay timeOfDay) {
            //reusing functionality from elsewhere... why not?
            return getHourEstimateFromTimeOfDay(timeOfDay);
        }
    };

    //introduce a 2 hr minimum stay
    private final Function<TimeOfDay, Integer> minDurationEstimateFunction = new Function<TimeOfDay, Integer>() {
        @Override
        public Integer apply(TimeOfDay timeOfDay) {
            //reusing functionality from elsewhere... why not?
            int estimate = getHourEstimateFromTimeOfDay(timeOfDay);
            return Math.max(estimate, 2);
        }
    };

    private final Function<GarageLevelSection, BigDecimal> simpleRateEstimateFunction = new Function<GarageLevelSection, BigDecimal>() {
        @Override
        public BigDecimal apply(GarageLevelSection container) {
            //reusing functionality from elsewhere... why not?
            //FIXME: Improvement Opportunity: instead of cheating, hourly rates can also be modeled as a MultiKeyMap with level and section acting as keys
            //(or rates can be modeled in a config (spring) or in a database table)
            return getHourlyRate(container.level, container.section);
        }
    };

    private final Function<GarageLevelSection, BigDecimal> saleRateEstimateFunction = new Function<GarageLevelSection, BigDecimal>() {
        @Override
        public BigDecimal apply(GarageLevelSection container) {
            //offer a 15% discount
            //FIXME: Improvement Opportunity: instead of cheating, hourly rates can also be modeled as a MultiKeyMap with level and section acting as keys
            //(or rates can be modeled in a config (spring) or in a database table)
            return getHourlyRate(container.level, container.section).multiply(new BigDecimal("0.85"));
        }
    };

    /**
     * Everything is done using guava functions. Again, this may look contrived - not everything requires functional
     * idioms and sometimes such programming style can do more harm than good. For the purposes of the example we go all
     * functions!
     * 
     * @param request
     * @return
     */
    TicketResponse evaluateFunctionally(TicketRequest request) {
        log.info("Processing functionally: " + request);
        Car car = new Car(CarType.fromModel(request.modelName), request.makeYear);

        //can be further refactored into a helper Builder class
        BusinessRule bizRule = getBizRule(car, request.entryTimestamp);
        TimeOfDay timeOfDay = bizRule.timeOfDayFunction.apply(request.entryTimestamp);
        GarageLevelSection glsReco1 = getRecommendation(car.getCarClass(), timeOfDay, 1);
        HourlyEstimate estimate1 = new HourlyEstimate(bizRule.durationEstimateFunction.apply(timeOfDay),
                bizRule.rateEstimateFunction.apply(glsReco1));
        Recommendation r1 = new Recommendation(glsReco1, estimate1);

        GarageLevelSection glsReco2 = getRecommendation(car.getCarClass(), timeOfDay, 2);
        HourlyEstimate estimate2 = new HourlyEstimate(bizRule.durationEstimateFunction.apply(timeOfDay),
                bizRule.rateEstimateFunction.apply(glsReco2));
        Recommendation r2 = new Recommendation(glsReco2, estimate2);
        return new TicketResponse(request.entryTimestamp, car, r1, r2);
    }

    //initialize method for constructing our rules
    //rules can be pulled from file config or from spring or from the db (and cached)
    //if using the Spring framework, this initialize method can be invoked by the init-method (post bean instantiation) interceptor
    void initialize() {
        initializeRecommendations();
        initializeBusinessRules();
    }

    //The use of int primitives is contrived but meant to illustrate that objects and primitives can be used
    //in combination to serve as a key into a multi-key map
    //Also note that there is nothing preventing your from returning single Functions with type erasure either...
    void initializeRecommendations() {
        RECOMMENDATIONS.put(CarClass.CLASSIC, TimeOfDay.MORNING, 1, new GarageLevelSection(GarageLevel.LEVEL1,
                GarageSection.SECTION1));
        RECOMMENDATIONS.put(CarClass.CLASSIC, TimeOfDay.MORNING, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION1));
        RECOMMENDATIONS.put(CarClass.CLASSIC, TimeOfDay.ANY, 1, new GarageLevelSection(GarageLevel.LEVEL1,
                GarageSection.SECTION2));
        RECOMMENDATIONS.put(CarClass.CLASSIC, TimeOfDay.ANY, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION2));

        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.MORNING, 1, new GarageLevelSection(GarageLevel.LEVEL3,
                GarageSection.SECTION1));
        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.MORNING, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION1));
        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.AFTERNOON, 1, new GarageLevelSection(GarageLevel.LEVEL3,
                GarageSection.SECTION2));
        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.AFTERNOON, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION2));
        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.EVENING, 1, new GarageLevelSection(GarageLevel.LEVEL3,
                GarageSection.SECTION3));
        RECOMMENDATIONS.put(CarClass.LUXURY, TimeOfDay.EVENING, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION3));

        RECOMMENDATIONS.put(CarClass.SPORT, TimeOfDay.EVENING, 1, new GarageLevelSection(GarageLevel.LEVEL3,
                GarageSection.SECTION3));
        RECOMMENDATIONS.put(CarClass.SPORT, TimeOfDay.EVENING, 2, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION3));
        RECOMMENDATIONS.put(CarClass.SPORT, TimeOfDay.ANY, 1, new GarageLevelSection(GarageLevel.LEVEL2,
                GarageSection.SECTION2));
        RECOMMENDATIONS.put(CarClass.SPORT, TimeOfDay.ANY, 2, new GarageLevelSection(GarageLevel.LEVEL3,
                GarageSection.SECTION2));

    }

    //could be lazy initialized and crude-cached on application startup, ie. spring's bean init override
    void initializeBusinessRules() {
        BIZ_RULES.put(CarClass.CLASSIC, new BusinessRule(complexDateConverterFunction, simpleDurationEstimateFunction,
                simpleRateEstimateFunction));

        BIZ_RULES.put(CarClass.LUXURY, new BusinessRule(complexDateConverterFunction, minDurationEstimateFunction,
                saleRateEstimateFunction));

        BIZ_RULES.put(CarClass.SPORT, new BusinessRule(simpleDateConverterFunction, simpleDurationEstimateFunction,
                saleRateEstimateFunction));

    }

    //simple abstraction for getting at the Function pointers encapsulated by a BusinessRule
    BusinessRule getBizRule(Car car, DateTime entryTime) {
        return BIZ_RULES.get(car.getCarClass());
    }

    //a more complex interaction with a multi-key map where we handle the absence of a rule for our inputs (null return value)
    //by pulling the pre-defined "default" rule.
    GarageLevelSection getRecommendation(CarClass carClass, TimeOfDay timeOfDay, int recoNum) {
        GarageLevelSection reco = (GarageLevelSection) RECOMMENDATIONS.get(carClass, timeOfDay, recoNum);
        //if time-specific rule doens't exist go for the default rule
        if (reco == null) {
            reco = (GarageLevelSection) RECOMMENDATIONS.get(carClass, TimeOfDay.ANY, recoNum);
        }
        Assert.notNull(reco, "Recommendations for " + carClass + ", " + timeOfDay + ", reco #" + recoNum
                + " is not defined.");
        return reco;
    }

    //really a container of Function "pointers"
    private static class BusinessRule {
        //could capture any behavior conforming to the contract - can include complex behavior such as DB or WebService calls.
        //elegantly encapsulates and abstracts away complex functionality
        final Function<DateTime, TimeOfDay> timeOfDayFunction;
        final Function<TimeOfDay, Integer> durationEstimateFunction;
        final Function<GarageLevelSection, BigDecimal> rateEstimateFunction;

        BusinessRule(Function<DateTime, TimeOfDay> timeOfDayFunction,
                Function<TimeOfDay, Integer> durationEstimateFunction,
                Function<GarageLevelSection, BigDecimal> rateEstimateFunction) {
            this.timeOfDayFunction = timeOfDayFunction;
            this.durationEstimateFunction = durationEstimateFunction;
            this.rateEstimateFunction = rateEstimateFunction;
        }
    }

    //helper object for storing both garage level and garage stage (since guava doesn't support multiple input arguments...  yet)
    private static class GarageLevelSection {
        final GarageLevel level;
        final GarageSection section;

        GarageLevelSection(GarageLevel level, GarageSection section) {
            this.level = level;
            this.section = section;
        }

        @Override
        public String toString() {
            return "GarageLevelSection [level=" + level + ", section=" + section + "]";
        }

        @Override
        public int hashCode() {
            return new HashCodeBuilder().append(this.level).append(this.section).hashCode();
        }

        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }
            if ((obj == null) || !(obj instanceof GarageLevelSection)) {
                return false;
            }
            GarageLevelSection other = (GarageLevelSection) obj;
            return new EqualsBuilder().append(this.level, other.level).append(this.section, other.section).isEquals();
        }
    }
}
