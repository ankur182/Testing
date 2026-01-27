private static boolean isInsurerMissing(
        Set<Insurance<Vessel, ?>> insurances,
        InsuranceType insuranceType) {

    return insurances.stream()
        .filter(i -> insuranceType.equals(i.getInsuranceType()))
        .map(Insurance::getContracts)
        .filter(Objects::nonNull)
        .flatMap(Collection::stream)
        .map(InsuranceContract::getInsurers)
        .filter(Objects::nonNull)
        .noneMatch(insurers -> !insurers.isEmpty());
}
